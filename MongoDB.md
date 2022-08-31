## Comandos para criação de coleções e registros - MongoDB

### Criar coleção

db.createCollection("alunos")
----------------------------------------------------------------

### Inserir na Collection
db.alunos.insert({
    "nome" : "Felipe",
    "data_nascimento" : new Date(1994,02,26)
}
)

### Query para inserção de diversos objetos na msm collection

db.alunos.insert(
	{
		"nome" : "Felipe",
		"data_nascimento" : new Date(1994,02,26),
		"curso":{
			"nome": "Sistemas da Informação"	
		},
		"notas" : [10.0,9.0,4.5],
		"habilidades":[
		{
			"nome":"inglês",
			"nível": "avançado"
		},
		{
			"nome":"taekwondo",
			"nível": "básico"
		}
		]
	}
)

### Query para trazer todos os resultados

db.alunos.find()

###Query para consultar os dados de forma mais "pretty"

db.alunos.find().pretty()

### No mongoDB, passamos objetos-exemplo para os dados que queremos buscar

db.alunos.find({"nome":"Felipe"})
-------------------------------------------------------------------------------------------
Por padrão, os elementos no mongo db não tem restrição de formato de inserção de parâmetros

db.alunos.insert(
	{
		"nome" : "Felipe",
		"curso":{
			"nome": "Matemática"	
		},
})


### Busca por atributos específicos internos

db.alunos.find(
	{   "nome":"Felipe",
            "habilidades.nome":"inglês"})

### Filtros de busca - or

db.alunos.find({
    $or: [
        {"curso.nome":"Sistemas da Informação"},
        {"curso.nome":"Engenharia Química"}
    ]
})
### Traz o nome de todos que estejam dentro dos cursos de sistemas de informação e engenharia química
db.alunos.find({
        {"curso.nome":{$ in: ["Sistemas da Informação",
        "Engenharia Química"]}
})

### Busca por critério e data de nascimento
db.alunos.find({"nome": "Felipe", "data_nascimento": new Date(1994, 02, 26)})

### Busca por in ou or
db.alunos.find({$or:
    [
        {"curso.nome": "Sistemas de informação"},
        {"curso.nome": "Ciências da computação"}

    ]
 }).pretty()

 OU

 db.alunos.find({"curso.nome": {$in:
    [
        "Sistemas de informação",
        "Ciências da computação"
    ]
 }}).pretty()
`
### Update total (substitui um documento por outro)

db.alunos.update({
"curso.nome":"Sistema de Informação"},
{
"curso.nome":"Sistemas da Informação"}
})		

### Update parcial (substitui em campos específicos) - só substitui o 1º elemento  q ele encontra
db.alunos.update(
    {"curso.nome":"Sistema de Informação"},
    {
        $set{
            "curso.nome":"Sistems da Informação"
        }
    }
)

### Update em varios elementos

db.alunos.update(
    {"curso.nome":"Sistema de Informação"},
    {
        $set: {
            "curso.nome":"Sistems da Informação"
        }
    },{multi:true}
)

### Colocar elemento dentro de uma array
db.alunos.update(
		{"_id" : ObjectId("630d54606980a1fa34de1899")},
		{
			$push: {
				notas: 8.5
			}
		
		}
)

### Dar um push de 1+ elemento dentro de uma array
db.alunos.update(
		{"_id" : ObjectId("630d54606980a1fa34de1899")},
		{
			$push: {
				notas:{$ each: [8.5,3] }
			}
		
		}
)

### Busca maior que(gt e gte)

db.alunos.find({
	"notas":{$gt :5}
})

### Busca somente 1 elemento maior que(gt e gte)

db.alunos.findOne({
	"notas":{$gt :5}
})

### Busca ordenada crescente

db.alunos.find().sort({
	"nome":1
})

### Busca ordenada decrescente
db.alunos.find().sort({
	"nome": -1
})

### Busca ordenada crescente limitada aos 3 primeros resultados
db.alunos.find().sort({
	"nome":1
}).limit(3)

### Busca por proximidade
db.alunos.update(
    {"_id" : ObjectId("630d1a1b6980a1fa34de1894")},
    {
        $set : {
            localizacao:{
				endereco:"Rua Vergueiro, 3185",
				cidade: "São Paulo",
				coordinates: [-23.588213, -46.632356],
				type: "Point"
			
			}
        }
    }
)

### Importar JSON

mongoimport - c alunos --jasonArray < alunos.json
mongoimport - mandando o db importar
- c alunos - para a collection alunos
--jasonArray - em um objeto jsonArray
< alunos.json - documento a ser importado

### Buscar por proximidade

db.alunos.aggregate([
{
	$geoNear : {
		near:{
			coordinates: [-23.588055, -46.632403],
			type: "Point"
		},
		distanceField: "distancia.calculada",
		spherical: true,
		num: 4
	}

},
{$ skip:1}
]) 

### Busca por proximidade passnado um index

db.alunos.createIndex({
	localizacao : "2dsphere"
})

https://www.mongodb.com/docs/mongodb-shell/crud/insert/
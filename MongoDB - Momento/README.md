# Momento 

**Contém a base de indicados da empresa Momento para treinar consultas complexas no MongoDB.**
**Vamos fazer algumas perguntas para brincar de análise exploratória de dados com MongoDB** <br>


**1. Quantos funcionarios da empresa Momento trabalham no departamento de vendas?** <br>
 - Trabalham 10 pessoas
   
```js
> db.funcionarios.countDocuments({departamento: ObjectId("85992103f9b3e0b3b3c1fe71")})

< 10
```

**2. Inclua suas próprias informações no departamento de Tecnologia da empresa.** <br>

```js
> db.funcionarios.insertOne(
{
"nome": "Lucas Miranda",
 "telefone": "534.777.7577",
 "email": "luquinhas@gmail.org",
 "dataAdmissao": "2000-12-20",
"cargo": "Web Developer",
"salario": 5000,
"departamento": ObjectId("85992103f9b3e0b3b3c1fe74")})

< {
  acknowledged: true,
  insertedId: ObjectId('66f18ef359a7898acde11741')
}
```

**3. Agora diga, quantos funcionários temos ao total na empresa?** <br>
   - Total de 24 pessoas
     
 ```js
> db.funcionarios.countDocuments()

< 24
```

**4. E quanto ao Departamento de Tecnologia?** <br>
   - Total de 6 pessoas
```js
> db.funcionarios.countDocuments({departamento: ObjectId("85992103f9b3e0b3b3c1fe74")})

< 6
```

**5. Qual a média salarial do departamento de tecnologia?** <br>
  - A média salarial é 4.560
```js
> db.funcionarios.aggregate([
  {$match:{departamento: ObjectId("85992103f9b3e0b3b3c1fe74")}},
  {$group: {
    "_id": null,
    "media": {$avg: "salario"}  }
  }
])

< {
  _id: null,
  media: 4560
}

```

**6. Quanto o departamento de Vendas gasta em salários?** <br>
  - Gasta 95.100
```js
> db.funcionarios.aggregate([
  {$match:{departamento: ObjectId("85992103f9b3e0b3b3c1fe71")}},
  {$group: {
    "_id": null,
    "media": {$sum: "salario"}
    }
  }
])

< {
  _id: null,
  soma: 95100
}
```

**7. Um novo departamento foi criado. O departamento de Inovações. Ele será locado no Brasil. Por favor, adicione-o no banco de dados da empresa colocando quaisquer informações que você achar relevantes.** <br>

```js
> db.departamentos.insertOne({_id: ObjectId("85992103f9b3e0b3b3c1fe77"),nome: "Inovacoes", 
escritorio: ObjectId("5f8b3f3f9b3e0b3b3c1e3e3e")
})

< {
  acknowledged: true,
  insertedId: ObjectId('85992103f9b3e0b3b3c1fe77')
}
```
**8. O departamento de Inovações está sem funcionários. Inclua alguns colegas de turma nesse departamento.** <br>

```js
> db.funcionarios.insertMany([{
'nome':'Lucas Miranda',
'telefone':'11 950392737',
'email':'miranda@gmail.com',
'cargo': 'Desenvolvedor Web',
'salario': 35000,
'departamento':ObjectId('5f8b3f3f9b3e0b3b3c1e3e3e')},
                            {
'nome': 'Kawan Turchiai',
'telefone': '11 9374957383',
'email': 'turchiai@gmail.com',
'cargo': 'Desenvolvedor Web', 
'salario': 30000,
'departamento': ObjectId('5f8b3f3f9b3e0b3b3c1e3e3e')
}])


< {
  acknowledged: true,
  insertedIds: {
    '0': ObjectId('6703c5af40321c74b97ce03d'),
    '1': ObjectId('6703c5af40321c74b97ce03e')
  }
}
```

**9. Quantos funcionarios a empresa Momento tem agora?** <br>
- 27 funcionarios

```js
> db.funcionarios.countDocuments()

< 27
```

**10. Quantos funcionários da empresa Momento possuem conjuges?** <br>
- 7 funcionarios possuem conjuges.
```js
> db.funcionarios.countDocuments({"dependentes.conjuge": {$exists: true}})

< 7 
```

**11. Qual a média salarial dos funcionários da empresa Momento, excluindo-se o CEO?** <br>

```js
> db.funcionarios.aggregate([
  {$match:{cargo: {$ne: "CEO"}}},
  {$group: {
    "_id": null,
    "total": {$avg: "$salario"}
    }
  }
])

< {
  _id: null,
  total: 9758.18
}
```

**12. Qual a média salarial do departamento de tecnologia?** <br>
- A média salarial é 4.633
```js
> db.funcionarios.aggregate([
    {
        $match: {
            departamento: ObjectId("85992103f9b3e0b3b3c1fe74")
        }
    },
    {
        $group: {
            _id: null,
            avg_val: { $avg: "$salario" }
        }
    }
])

< {
  _id: null,
  avg_val: 4633.333333333333
}
```

**13. Qual o departamento com a maior média salarial?** <br> 
- Departamento com Maior média salarial é o Executivo.
```js
> db.funcionarios.aggregate([
    {
        "$group": {
            "_id": "$departamento",
            "media_salario": { $avg: "$salario" }
        }
    },
    {
        "$lookup": {
            "from": "departamentos",
            "localField": "_id",            
            'foreignField': "_id",
            "as": "departamento"
        }
    },
    {
        "$sort": {"media_salario": -1}
    },
    {
        "$limit": 1
    },
    {
        "$project": {
            "_id": 0,
            "media_salario": 1,
             "departamento.nome": 1
        }
    }
])

< {
  media_salario: 71000,
  departamento: [
    {
      nome: 'Executivo'
    }
  ]
}

```
**14. Qual o departamento com o menor número de funcionários?** <br>
- Executivo (1x funcionario)

```js
> db.funcionarios.aggregate([
  {
    $group: {
      _id: "$departamento",
      numeroFuncionarios: { $sum: 1 }
    }
  },
  {
    $sort: { numeroFuncionarios: 1 } 
  },
  {
    $limit: 1
  },
{
        $lookup: {
            from: "departamentos", 
            localField: "_id", 
            foreignField: "_id", 
             as: "departamentoInfo"  
        }
    },
    {
        $unwind: "$departamentoInfo"  
    },
    {
        $project: {
            _id: 0,  
            departamento: "$departamentoInfo.nome",  
            numeroFuncionarios: 1  
        }
    }
])

< {
  numeroFuncionarios: 1,
  departamento: 'Executivo'
}
```

**15.Pensando na relação quantidade e valor unitario, qual o produto mais valioso da empresa?** <br>
- Sabre de Luz (Mace Windu) - Total: 7922.32
```js
> db.vendas.aggregate([
{ 
   $group: {
    _id:"$produto", total: {$sum: {$multiply: ["$precoUnitario", "$quantidade"]}}
}
},
{
    $sort: {total: -1}
},
{
     $limit: 1
}
])


< {
  _id: 'Sabre de Luz (Mace Windu)',
  total: 7922.32
}

```

# Oscar


1- Quantas vezes Natalie Portman foi indicada ao Oscar? <br>
Foi indicada 3x.
```js
> db.oscar.countDocuments({nome_do_indicado: /Natalie Portman/})
< 3
```

2- Quantos Oscars Natalie Portman ganhou? <br>
Ganhou 1 oscar.
```js
> db.oscar.countDocuments({nome_do_indicado: /Natalie Portman/, vencedor: "true"})
< 1
```

3- Amy Adams já ganhou algum Oscar? <br>
Não ganhou oscar.
```js
> db.oscar.countDocuments({nome_do_indicado: /Amy Adams/, vencedor: "true"})
< 0
```

4- A série de filmes Toy Story ganhou um Oscar em quais anos? <br>
Ganhou Oscar entre 2011 a 2020.
```js
> db.oscar.find({nome_do_filme: /Toy Story/i, vencedor: 1}, {nome_do_filme: 1, ano_cerimonia: 1, _id: 0})
< {
  ano_cerimonia: 2011,
  nome_do_filme: 'Toy Story 3'
}
{
  ano_cerimonia: 2011,
  nome_do_filme: 'Toy Story 3'
}
{
  ano_cerimonia: 2020,
  nome_do_filme: 'Toy Story 4'
}
```

5- A partir de que ano que a categoria "Actress" deixa de existir? <br>
A partir do ano 1931.

```js
db.oscar.find({"categoria": /ACTRESS/i}, {categoria:1, ano_filmagem: 1, ano_cerimonia: 1})
> 1931

```


6- O primeiro Oscar para melhor Atriz foi para quem? Em que ano? <br>
Foi para Janet Gaynor. Ano de 1927
```js
> db.oscar.find({vencedor:"true", categoria: "ACTRESS"}, {ano_filmagem: 1, nome_do_indicado: 1, _id: 0}).sort({ano_filmagem: 1}).limit(1)
< {
  ano_filmagem: 1927,
  nome_do_indicado: 'Janet Gaynor'
}
}
```

7- Na campo "Vencedor", altere todos os valores com "Sim" para 1 e todos os valores "Não" para 0. <br>

```js
> db.oscar.updateMany({vencedor: "true"}, {$set: {vencedor: 1}})
< {
  acknowledged: true,
  insertedId: null,
  matchedCount: 2464,
  modifiedCount: 2464,
  upsertedCount: 0
}

> db.oscar.updateMany({vencedor: "false"}, {$set: {vencedor: 0}})
< {
  acknowledged: true,
  insertedId: null,
  matchedCount: 8423,
  modifiedCount: 8423,
  upsertedCount: 0
}
```

8- Em qual edição do Oscar "Crash" concorreu ao Oscar? <br>
A 78.ª edição do Oscar

```js
> db.oscar.find({nome_do_filme: "Crash"}, {nome_do_filme: 1, ano_cerimonia: 1, cerimonia: 1, _id:0}).sort({cerimonia: 1}).limit(1)
< {
  ano_cerimonia: 2006,
  cerimonia: 78,
  nome_do_filme: 'Crash'
}

```

9- Bom... dê um Oscar para um filme que merece muito, mas não ganhou. <br>
Toy Story 3.

```js
> db.oscar.updateMany({nome_do_filme: /Toy Story 3/i, vencedor: 0}, {$set:{vencedor: 1}})
< {
  acknowledged: true,
  insertedId: null,
  matchedCount: 3,
  modifiedCount: 3,
  upsertedCount: 0
}

> db.oscar.find({nome_do_filme: /Toy Story 3/i})
{
  _id: ObjectId('66ead38c6c024a36e96bfa15'),
  id_registro: 9165,
  ano_filmagem: 2010,
  ano_cerimonia: 2011,
  cerimonia: 83,
  categoria: 'ANIMATED FEATURE FILM',
  nome_do_indicado: 'Lee Unkrich',
  nome_do_filme: 'Toy Story 3',
  vencedor: 1
}

```

10- O filme Central do Brasil aparece no Oscar? <br>
Não

```js
> db.oscar.find({nome_do_filme: /Central do Brasil/i, vencedor: 1})
< 
```

11- Inclua no banco 3 filmes que nunca foram nem nomeados ao Oscar, mas que merecem ser. <br>

```js
> db.oscar.insertMany([
    {
      "id_registro": 10890,
          "ano_filmagem": 2022,
          "ano_cerimonia": "NULL",
          "cerimonia": "NULL",
          "categoria": "NULL",
          "nome_do_indicado": "NULL",
          "nome_do_filme": "Suzume",
          "vencedor": 0
    },
  {
    "id_registro": 10891,
        "ano_filmagem": 2007,
        "ano_cerimonia": 2008,
        "cerimonia": "NULL",
        "categoria": "Best Actor",
        "nome_do_indicado": "Heath Ledger was nominated for Best Supporting Actor.",
        "nome_do_filme": "O Cavaleiro das Trevas",
        "vencedor": 0
  },

  {
    "id_registro": 10892,
        "ano_filmagem": 2009,
        "ano_cerimonia": 2011,
        "categoria": "",
        "nome_do_indicado": "",
        "nome_do_filme": "Lanterna Verde",
        "vencedor": 0
  }
])

< {
  acknowledged: true,
  insertedIds: {
    '0': ObjectId('66eb20525f40023bfdb9bc16'),
    '1': ObjectId('66eb20525f40023bfdb9bc17'),
    '2': ObjectId('66eb20525f40023bfdb9bc18')
  }
}

```

12 - Pensando no ano em que você nasceu: Qual foi o Oscar de melhor filme, Melhor Atriz e Melhor Diretor? <br>

```js
> db.oscar.find({ano_filmagem: 2005, categoria: /ACTRESS/i, vencedor: 1})
< {
  _id: ObjectId('66ead38c6c024a36e96bf7cd'),
  id_registro: 8581,
  ano_filmagem: 2005,
  ano_cerimonia: 2006,
  cerimonia: 78,
  categoria: 'ACTRESS IN A LEADING ROLE',
  nome_do_indicado: 'Reese Witherspoon',
  nome_do_filme: 'Walk the Line',
  vencedor: 1
}

> db.oscar.find({ano_filmagem: 2005, categoria: /ACTOR/, vencedor: 1})
< {
  _id: ObjectId('66ead38c6c024a36e96bf7c4'),
  id_registro: 8572,
  ano_filmagem: 2005,
  ano_cerimonia: 2006,
  cerimonia: 78,
  categoria: 'ACTOR IN A SUPPORTING ROLE',
  nome_do_indicado: 'George Clooney',
  nome_do_filme: 'Syriana',
  vencedor: 1
}

> db.oscar.find({ano_filmagem: 2005, categoria: /ACTOR/i, vencedor: 1})
< {
  _id: ObjectId('66ead38c6c024a36e96bf7bf'),
  id_registro: 8567,
  ano_filmagem: 2005,
  ano_cerimonia: 2006,
  cerimonia: 78,
  categoria: 'ACTOR IN A LEADING ROLE',
  nome_do_indicado: 'Philip Seymour Hoffman',
  nome_do_filme: 'Capote',
  vencedor: 1
}
```

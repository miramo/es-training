---
theme: apple-basic
title: Elasticsearch Training
author: Maxime Miramond
layout: image
image: https://images.contentstack.io/v3/assets/bltefdd0b53724fa2ce/blt7e69fae920c5d2c7/60770b6cdbce59715a7d3a2f/blog-thumbnail-generic-elastic.png
favicon: https://www.elastic.co/favicon-32x32.png
fonts:
  sans: 'Roboto'
  serif: 'Roboto Slab'
  mono: 'Fira Code'
---

<div class="absolute top-5 right-5 text-blue-900">
  <span class="font-700 text-xs">
    Le dev au fond 🧑‍💻, près de la fenêtre 🪟
  </span>
</div>

<div class="absolute bottom-5">
  <h1>Introduction to Elasticsearch</h1>
  <p>For beginners, by a beginner</p>
</div>

---
transition: fade-out
---

## Elastic Stack

<img src="https://github.com/miramo/es-training/blob/main/assets/images/elk-stack.png?raw=true" style="display:block;float:none;margin-left:auto;margin-right:auto;height:90%">

<!--
ELK est une suite open source comprenant 3 composants principaux : Elasticsearch, Logstash et Kibana.
Beats a ensuite été ajouté pour former la stack ELK. ELK permet l’indexation et l’analyse de données.
Vous pourrez par exemple charger différents types de données, comme vos logs, et les visualiser sous forme de diagrammes personnalisés.
En plus des fonctionnalités énumérées ci-dessous, il peut également faire office de SIEM pour gérer les alertes de sécurité.
-->

---
transition: fade-out
layout: image-right
image: https://i.kym-cdn.com/photos/images/original/000/538/716/7f5.gif
---

## Key Features

<br><br>

- 🔎 Real-time search and analytics
- 📈 Scalability
- ✔️ High availability
- 🔓 Open-source
- 🔄 Flexible data models

---
transition: fade-out
layout: image-left
image: https://i.giphy.com/media/NBb0DtalEEyMU/giphy.webp
---

## Un peu de terminologie

| **Relational Database** | **Elasticsearch** |
|-------------------------|-------------------|
| Database                | Index             |
| Table                   | Type              |
| Row                     | Document          |
| Column                  | Field             |
| Schema                  | Mapping           |
| SQL                     | Query DSL         |

<br>

<a href="https://www.elastic.co/guide/en/elasticsearch/reference/current/removal-of-types.html#_schedule_for_removal_of_mapping_types" target="_blank">
    💡 elastic will remove types
</a>

<!--
types is deprecated in 7.x and removed in 8.x

Même si on peux faire une comparaison avec une base de données relationnelle, il ne faut pas oublier que ce n'est pas une base de données "primaire".
On s'en sert généralement pour maintenir une projection de différentes sources.
-->

---
transition: fade-out
---

## Run Elasticsearch

<br>

```bash
docker pull elasticsearch:8.8.1
```

```bash
docker run -d --rm --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e "xpack.security.enabled=false" elasticsearch:8.8.1
```

<br>

<v-clicks>
<img src="https://i.giphy.com/media/3o7btNa0RUYa5E7iiQ/giphy.webp" style="display:block;float:none;margin-left:auto;margin-right:auto;width:33%">
</v-clicks>


---
transition: fade-out
layout: two-cols
---

<template v-slot:default>

## API RESTful

<br>
Permet notamment de :

<v-clicks>

- 📂 Indexer un document
- 🔍 Rechercher un document
- ♻️ Mettre à jour un document
- 🗑️ Supprimer un document

</v-clicks>

<br><br>

<v-clicks>

<a href="https://www.elastic.co/guide/en/elasticsearch/reference/current/rest-apis.html" target="_blank">
    elastic.co/rest-apis.html
</a>

</v-clicks>

</template>
<template v-slot:right>

<v-clicks>
<img src="https://s3.memeshappen.com/memes/Who-is-JaSON-And-why-is-he-stateless-meme-46010.jpg">
</v-clicks>

</template>

---
transition: fade-in
---

## Création d'un index

L'URL de base se présente de la manière suivante

> http://localhost:9200/_index/_doc/_id

<br>

👉 Ajoutons un index pour stocker des informations sur les voitures Tesla

<v-clicks>
```bash
curl -XPUT 'http://localhost:9200/tesla/'
```
</v-clicks>

<v-clicks>
<div class="mt-10 flex justify-center items-center">
    <Tweet id="1519480761749016577" scale="0.7" />
</div>
</v-clicks>

---
transition: slide-out
---

## Mapping

👉 Ajoutons un mapping pour définir la structure de nos documents

<v-clicks>
```bash
curl -XPUT -H 'Content-Type: application/json' 'http://localhost:9200/tesla/_mapping' -d '
{
  "properties": {
    "name": {
      "type": "text"
    },
    "code": {
      "type": "keyword"
    },
    "description": {
      "type": "text"
    }
  }
}'
```
</v-clicks>

<div v-click>
Quand utiliser un type "text" ou "keyword" ? 🤔
</div>

<v-clicks>

- "text" : pour les champs qui doivent être analysés (recherche full-text)
- "keyword" : pour les champs qui ne doivent pas être analysés (recherche exacte)

</v-clicks>

<!--
Lorsque vous indexez un document sans mapping, Elasticsearch en crée automatiquement un pour vous en se basant sur les types de données des champs du document.
Faire attention, par défaut une string sera indexé en text, ce qui n'a aucuns sens pour un champ de type uuid par exemple.
Dans l'idéal, il faut toujours définir un mapping explicite pour chaque index.
-->

---
transition: slide-out
---

## Indexation d'un document

👉 Ajoutons quelques models Tesla

<v-clicks>
```bash
curl -XPUT -H 'Content-Type: application/json' 'http://localhost:9200/tesla/_doc/1' -d '
{
  "name": "Model S",
  "code": "S",
  "description": "LA berline électrique"
}'
```
</v-clicks>

<v-clicks>
```bash
curl -XPUT -H 'Content-Type: application/json' 'http://localhost:9200/tesla/_doc/2' -d '
{
  "name": "Model 3",
  "code": "3",
  "description": "La voiture électrique des devs"
}'
```
</v-clicks>

<v-clicks>
```bash
curl -XPUT -H 'Content-Type: application/json' 'http://localhost:9200/tesla/_doc/3' -d '
{
  "name": "Model X",
  "code": "X",
  "description": "Le SUV des michel qui ont trop de moula"
}'
```
</v-clicks>

---
transition: slide-in
---

## Recherche dans Elasticsearch

### > Où est-ce que j'ai garé ma Model S Plaid ? 🤔

👉 Ah, faisons une recherche rapide !

<v-clicks>

```bash
curl -XGET 'http://localhost:9200/tesla/_search?q=code:S'
```

</v-clicks>

<br>

<v-clicks>
<img src="https://media.tenor.com/l4ovlwYCwIkAAAAd/mkbhd-mini-tesla-mkbhd-tesla-pc.gif" style="display:block;float:none;margin-left:auto;margin-right:auto;width:33%">
</v-clicks>

---
transition: slide-in
layout: two-cols-header
clicks: 4
---

## Recherche dans Elasticsearch

::left::

<div style="width: 95%">

```json {all|12|18|19|21-25} {maxHeight:'400px'}
{
  "took":3,
  "timed_out":false,
  "_shards":{
    "total":1,
    "successful":1,
    "skipped":0,
    "failed":0
  },
  "hits":{
    "total":{
      "value":1,
      "relation":"eq"
    },
    "max_score":1.2039728,
    "hits":[
      {
        "_index":"tesla",
        "_id":"1",
        "_score":1.2039728,
        "_source":{
          "name":"Model S",
          "code":"S",
          "description":"LA berline électrique"
        }
      }
    ]
  }
}
```

</div>

::right::

<div v-click="1">

### ... nombre total de hits

</div>
<div v-click="2">

### ... l'index d'où il provient

</div>
<div v-click="3">

### ... l'id du document

</div>
<div v-click="4">

### ... la source du document

</div>

---
transition: slide-out
---

## Requêtes plus complexes avec Query DSL

<v-clicks>

- Requête "bool" : Combine plusieurs requêtes avec des opérateurs logiques
  - "must" : Tous les critères doivent être vérifiés (AND)
  - "should" : Au moins un critère doit être vérifié (OR)
  - "must_not" : Aucun critère ne doit être vérifié (NOT)
  - "filter" : Comme "must" mais ne contribue pas au score

- Requête "match" : Recherche basique correspondant aux termes dans les champs des documents

- Requête "multi_match" : Recherche un terme dans plusieurs champs différents

- Requête "aggregations" : Regroupe les données et fournit des statistiques sur ces groupes ("GROUP BY", "COUNT", "SUM", "AVG", "MIN", "MAX")

- Et bien plus encore...

- <a href="https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html" target="_blank">elastic.co/query-dsl.html</a>

</v-clicks>

<!--
Must : C'est comme dire "Je veux absolument ça". Si vous mettez des conditions dans must, toutes ces conditions doivent être satisfaites. C'est l'équivalent d'un "AND" en SQL.

Filter : Imaginez que vous avez un groupe de résultats, mais vous voulez seulement ceux qui répondent à certaines conditions. Filter est utile pour cela. Les conditions que vous mettez sous filter agissent comme un post-filtre. De plus, le scoring n'est pas affecté par le filter.

Should : C'est comme dire "Ce serait bien si j'avais ça aussi". Si vous mettez des conditions dans should, elles ne sont pas indispensables mais si elles sont remplies, cela améliore le score (relevance score) de votre résultat.
-->

---
transition: slide-in
---

## Exemple de requête "match"

👉 Allons à la recherche de notre Model S !

```bash
curl -XGET -H 'Content-Type: application/json' 'http://localhost:9200/tesla/_search' -d '
{
  "query": {
    "match": {
      "code": "S"
    }
  }
}'
```

<br>

<v-clicks>
<img src="https://media.tenor.com/1sYGfNsOUbYAAAAC/jamie-foxx-elon-musk.gif" style="display:block;float:none;margin-left:auto;margin-right:auto;width:33%">
</v-clicks>

---
transition: slide-out
---

## Exemple de requête "bool"

👉 Et si on cherchait une Model S ou un Model 3 ?

```bash
curl -XGET -H 'Content-Type: application/json' 'http://localhost:9200/tesla/_search' -d '
{
  "query": {
    "bool": {
      "should": [
        { "match": { "code": "S" } },
        { "match": { "code": "3" } }
      ]
    }
  }
}'
```

<br>

<v-clicks>
<img src="https://media.tenor.com/SFjuTbTx4-oAAAAC/tugcekulas-elon-musk.gif" style="display:block;float:none;margin-left:auto;margin-right:auto;width:25%">
</v-clicks>
  
---
transition: slide-in
---

## Exemple de requête "multi_match"

👉 Recherchons tous les modèles Tesla qui ont 'model' dans leur nom ou leur description

```bash
curl -XGET -H 'Content-Type: application/json' 'http://localhost:9200/tesla/_search' -d '
{
  "query": {
    "multi_match": {
      "query": "model",
      "fields": ["name", "description"]
    }
  }
}'
```

<br>

<v-clicks>
<img src="https://media.tenor.com/UnqPvAxUphUAAAAC/elon-musk.gif" style="display:block;float:none;margin-left:auto;margin-right:auto;width:25%">
</v-clicks>

---
transition: slide-out
---

## Exemple de requête avec "aggregations"

👉 Regardons combien de modèles Tesla nous avons dans notre index

```bash
curl -XGET -H 'Content-Type: application/json' 'http://localhost:9200/tesla/_search' -d '
{
  "size": 0,
  "aggs": {
    "group_by_code": {
      "terms": {
        "field": "code"
      }
    }
  }
}'
```

<br>

<v-clicks>
<img src="https://media.tenor.com/6k-VJp84mMoAAAAC/elon-musk-too-much.gif" style="display:block;float:none;margin-left:auto;margin-right:auto;width:25%">
</v-clicks>

---
transition: slide-out
layout: two-cols-header
clicks: 6
---

## Pro tips 🎉

::left::

### > J'comprend R frère 🤯

<v-clicks>

### > Tkt, on va appeler le S 💯

</v-clicks>

<v-clicks>

👉 Utilise l'API de traduction SQL vers Query DSL

</v-clicks>

<v-clicks>

<div style="width: 98%">

```bash
POST _sql/translate?pretty --data-binary $'
{
  "query": "SELECT name FROM tesla 
      WHERE (MATCH(name, \'model\')
      OR MATCH(description, \'model\'))
      AND code = 3",
  "fetch_size": 5
}'
```

</div>

</v-clicks>

<div v-click="5">

👉 Ouuuuu, demande a ChatGPT 😎

</div>

::right::

<div v-click="4">

```json {all} { maxHeight: '410px' }
{
  "size" : 5,
  "query" : {
    "bool" : {
      "must" : [
        {
          "bool" : {
            "should" : [
              {
                "match" : {
                  "name" : {
                    "query" : "model"
                  }
                }
              },
              {
                "match" : {
                  "description" : {
                    "query" : "model"
                  }
                }
              }
            ],
            "boost" : 1.0
          }
        },
        {
          "term" : {
            "code" : {
              "value" : 3
            }
          }
        }
      ],
      "boost" : 1.0
    }
  },
  "_source" : false,
  "fields" : [
    {
      "field" : "name"
    }
  ],
  "sort" : [
    {
      "_doc" : {
        "order" : "asc"
      }
    }
  ],
  "track_total_hits" : -1
}
```

</div>

<div class="absolute bottom-0px" v-click="6">
  <img src="https://i.imgflip.com/82c55c.jpg" style="width:75%">
</div>

---
transition: slide-out
---

## Les analyzers

👉 Quand on indexe un document, Elasticsearch analyse le texte et le stocke dans un format optimisé pour la recherche.

<v-clicks>

Prenons l'exemple suivant :

```bash
curl -XPUT -H 'Content-Type: application/json' 'http://localhost:9200/tesla/_doc/4' -d '
{
  "name": "Model Y",
  "code": "Y",
  "description": "Le SUV compact"
}'
```
</v-clicks>

<v-clicks>

- Le champ "name" est analysé et stocké sous la forme "model" et "y"
- Le champ "description" est analysé et stocké sous la forme "suv" et "compact"
- Le champ "code" n'est pas analysé et stocké sous la forme "Y"
- Le champ "code" est donc plus adapté pour une recherche exacte
- Le champ "name" et "description" sont plus adaptés pour une recherche full-text
- <a href="https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis.html" target="_blank">elastic.co/analysis.html</a>

</v-clicks>

---
transition: slide-out
---

## L’importance de l’analyse

👉 Prenons l'exemple suivant :

<v-clicks>

```bash
{
  "description": "Outre la recharge à domicile et sur bornes ordinaires, la Model 3
  dispose de bornes de recharges ultra-rapides appelées « Superchargeurs ». Elles permettent
  de récupérer 80 % de la charge en moins de 30 min ou 300 km en moins de 15 min sur la Model 3."
}
```

Avec l'analyse par défaut d'Elasticsearch, le champ "description" sera analysé et stocké sous la forme suivante :

```bash
{
  "description": ["outre", "recharge", "domicile", "borne", "ordinaire", "model", "superchargeur", "continent", "amerique", "nord", "europe", "asie", "oceanie", "permettre", "recuperer", "charge", "min", "km", "min", "model"]
}
```

</v-clicks>

<v-clicks>

- Lors d'une recherche, les termes recherchés sont analysés aussi, avec la même technique
- Si on recherche "Bornes" au pluriel, on ne trouvera pas de résultat
- Le token obtenu serait "bornes", qui n'existe pas dans notre index
- L’utilisateur va donc devoir saisir les mots exacts : avec pluriels, accents, ligature…
- Pas acceptable !

</v-clicks>

---
transition: slide-out
---

## L’importance de l’analyse

👉 Comment faire pour que la recherche soit plus permissive ?

<div v-click>

En utilisant l’analyzer french d’Elasticsearch, les tokens seront plutôt :
    
```bash
{
  "description": ["recharg", "domicil", "born", "ordinair", "model", "superchargeur", "continent", "ameriqu", "nord", "europ", "asi", "ocean", "permettr", "recuper", "charg", "min", "km", "min", "model"]
}
```

</div>

<v-clicks>

- Mieux, maintenant "bornes" est analysé et stocké sous la forme "born"
- On peut donc rechercher "bornes" ou "borne" et obtenir le même résultat
- <a href="https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-lang-analyzer.html" target="_blank">elastic.co/analysis-lang-analyzer.html</a>

</v-clicks>

---
transition: slide-out
layout: two-cols-header
clicks: 10
---

## Les différentes étapes de l’analyse

::left::

<div style="width: 95%">

```json {all|all|all|all|25|26-32|27|28|29|30|31} {maxHeight:'400px'}
{
  "settings": {
    "analysis": {
      "filter": {
        "french_elision": {
          "type":         "elision",
          "articles_case": true,
          "articles": [
              "l", "m", "t", "qu", "n", "s",
              "j", "d", "c", "jusqu", "quoiqu",
              "lorsqu", "puisqu"
            ]
        },
        "french_stop": {
          "type":       "stop",
          "stopwords":  "_french_"
        },
        "french_stemmer": {
          "type":       "stemmer",
          "language":   "light_french"
        }
      },
      "analyzer": {
        "french": {
          "tokenizer":  "icu_tokenizer",
          "filter": [
            "lowercase",
            "french_elision",
            "french_stop",
            "french_stemmer",
            "icu_folding"
          ]
        }
      }
    }
  }
}
```

</div>

::right::

<div style="width: 95%" class="text-xs">

<v-clicks at="0">

- **Character filter** : Supprime les caractères non désirés (ex : html_strip, mapping, pattern_replace)
- **Tokenizer** : Découpe le texte en tokens
- **Token filter** : Modifie les tokens obtenus

</v-clicks>

<div v-click="4">

- **tokenizer** par défaut : basique
- **icu_tokenizer** : basé sur la librairie ICU, gère efficacement le standard Unicode

</div>

<v-clicks at="4">

Les **token filters** permettent de modifier ces tokens :

</v-clicks>

<v-clicks at="5">

- **"lowercase"** transforme tous les tokens en minuscules
- **"elision"** supprime les élisions (ex : "l'homme" devient "homme")
- **"stop"** élimine les tokens considérés comme du bruit (ex : en, au, du, par, est)
- **"stemmer"** réduit les mots à leur racine (ex : "marche", "marches", "marcher", "marchera" deviennent "march")
- **"folding"** supprime les accents (ex : "école" devient "ecole")

</v-clicks>

</div>

<!--
Le tokenizer par défaut est basique, tandis que l'icu_tokenizer, basé sur la librairie ICU, gère efficacement le standard Unicode.
-->

---
transition: slide-out
---

## Pipelines

👉 Les pipelines permettent de définir une suite d'opérations à effectuer sur un champ lors de l'indexation.

<v-clicks>

Utiles pour :

</v-clicks>

<v-clicks>

- Modifier/manipuler les données avant l'indexation
- Enrichir les données avec des informations supplémentaires
- Supprimer des données non désirées

</v-clicks>

<br>

<v-clicks>

Vous pouvez les voir comme un moyen de transformer et enrichir vos données "à la volée" lors de l'indexation.

<a href="https://www.elastic.co/guide/en/elasticsearch/reference/current/ingest.html" target="_blank">elastic.co/pipeline.html</a>

<img src="https://media.tenor.com/e5TDUiAGEowAAAAC/pipes-bursting.gif" style="display:block;float:none;margin-left:auto;margin-right:auto;width:20%">

</v-clicks>

---
transition: slide-out
---

## Crée un pipeline

👉 Créons un pipeline pour enrichir nos données Tesla

```bash {all|6-9|12-15}
curl -XPUT -H 'Content-Type: application/json' 'http://localhost:9200/_ingest/pipeline/tesla-pipeline' -d '
{
  "description": "Pimp my Tesla",
  "processors": [
    {
      "set": {
        "field": "brand",
        "value": "Tesla"
      }
    },
    {
      "set": {
        "field": "created_at",
        "value": "{{_ingest.timestamp}}"
      }
    }
  ]
}'
```

---
transition: slide-out
---

## Ajouter un pipeline à l'indexation

👉 Ajoutons notre pipeline à l'indexation

<v-clicks>

```bash
curl -XPUT -H 'Content-Type: application/json' 'http://localhost:9200/tesla/_doc/4?pipeline=tesla-pipeline' -d '
{
  "name": "Model Y",
  "code": "Y",
  "description": "Le SUV compact"
}'
```

</v-clicks>

<v-clicks>

Ce qui nous donne :

</v-clicks>

<v-clicks>

```json {8,10}
{
   "_index":"tesla",
   "_id":"4",
   "_score":0.6931471,
   "_source":{
      "name":"Model Y",
      "description":"Le SUV compact",
      "created_at":"2023-09-15T18:21:04.476592889Z",
      "code":"Y",
      "brand":"Tesla"
   }
}
```

</v-clicks>

<!--
Ce qui a par exemple été utile lors de la migration d'ES 6.x vers 8.x sur IPA.
Où il n'était plus possible d'indéxer des champs de type "geo_shape" avec un input de type "circle, radius".
On a donc du introduire un pipeline pour transformer le point/radius en polygone.
-->

---
transition: fade-out
---

## Let’s practice!

💻 <a href="https://darling-seal-terribly.ngrok-free.app/app/dev_tools" target="_blank">go.gojob.com/eskibana</a>

📚 <a href="https://www.notion.so/gojob-interim/Elasticsearch-Training-Template-1042d7f88eb5436087189acdf10deaed" target="_blank">go.gojob.com/esexo</a>

<br>

<img src="https://i.giphy.com/media/3oKIPnAiaMCws8nOsE/giphy.webp" style="display:block;float:none;margin-left:auto;margin-right:auto;width:35%">

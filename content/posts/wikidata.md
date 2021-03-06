---
title: "Querying WikiData with SPARQL"
date: 2020-05-27T18:54:56+02:00
categories:
  - Data
---

I'm working on Entity Linking and Knowledge Bases.
In that context, exporting a relevant part of Wikidata can be really useful to build surface form dictionaries and coocurence probabilities etc...
In order to know which part of Wikidata is relevant to dump, I thought we could query Wikidata (although it seems we can only download the entire dump and filter afterwards).

There is DSL for querying Wikidata called `SPARQL`.
At first sight, the syntax is not particularly easy and I've gone through this [tutorial](https://m.wikidata.org/wiki/Special:MyLanguage/Wikidata:SPARQL_tutorial).

Here are my solution to the exercises in that tutorial.

There are many namespaces in SPARQL : `wd`, `wdt`, `p`, `ps`, `pq`, `prov`

## Chemical elements

Write a query that returns all chemical elements with their element symbol and atomic number, in order of their atomic number.

```sql
SELECT ?element ?elementLabel ?symbol ?atomic_number
WHERE
{
  ?element wdt:P31 wd:Q11344;
           wdt:P246 ?symbol ;
           wdt:P1086 ?atomic_number .
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE]". }
}
ORDER BY ASC(?atomic_number)
```

[Try it](https://query.wikidata.org/#SELECT%20%3Felement%20%3FelementLabel%20%3Fsymbol%20%3Fatomic_number%0AWHERE%0A%7B%0A%20%20%3Felement%20wdt%3AP31%20wd%3AQ11344%3B%0A%20%20%20%20%20%20%20%20%20%20%20wdt%3AP246%20%3Fsymbol%20%3B%0A%20%20%20%20%20%20%20%20%20%20%20wdt%3AP1086%20%3Fatomic_number%20.%0A%20%20SERVICE%20wikibase%3Alabel%20%7B%20bd%3AserviceParam%20wikibase%3Alanguage%20%22%5BAUTO_LANGUAGE%5D%22.%20%7D%0A%7D%0AORDER%20BY%20ASC%28%3Fatomic_number%29)

## Rivers that flow into the Mississippi

Write a query that returns all rivers that flow directly or indirectly into the Mississippi River.

```sql
SELECT ?river ?riverLabel
WHERE
{
  ?river wdt:P31 wd:Q4022;
         wdt:P403/wdt:P403* wd:Q1497 .
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE]". }
}
ORDER BY ASC(?riverLabel)
```

[Try it](https://query.wikidata.org/#SELECT%20%3Friver%20%3FriverLabel%0AWHERE%0A%7B%0A%20%20%3Friver%20wdt%3AP31%20wd%3AQ4022%3B%0A%20%20%20%20%20%20%20%20%20wdt%3AP403%2Fwdt%3AP403%2a%20wd%3AQ1497.%0A%20%20SERVICE%20wikibase%3Alabel%20%7B%20bd%3AserviceParam%20wikibase%3Alanguage%20%22%5BAUTO_LANGUAGE%5D%22.%20%7D%0A%7D%0AORDER%20BY%20ASC%28%3FriverLabel%29%0A%0A%0A)

## References to Le Figaro website

```sql
SELECT ?ref ?refURL WHERE {
  ?ref pr:P854 ?refURL .
  FILTER (CONTAINS(str(?refURL),'lefigaro.fr')) .
} LIMIT 10
```

[Try it](https://query.wikidata.org/#SELECT%20%3Fref%20%3FrefURL%20WHERE%20%7B%0A%20%20%3Fref%20pr%3AP854%20%3FrefURL%20.%0A%20%20FILTER%20%28CONTAINS%28str%28%3FrefURL%29%2C%27lefigaro.fr%27%29%29%20.%20%20%20%20%20%20%20%0A%7D%20LIMIT%2010)

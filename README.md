# posos-backend-challenge

Welcome on this not-so-short test that aims to help us evaluate your backend development skills.
As it may be a bit long to answer, full completion is not mandatory. Write down the amount of time you spent on each tasks and features along a small dev log to give us a glimpse of your workflow.

## Context

POSOS has a pseudo-REST API to expose its Natural Entity Recognition & Linking (NERL) service. This is one of the core POSOS services: it extracts known medical entities from a text and links them to common ontologies.

> If given the string "Doliprane chez la femme enceinte", the NERL API will detect "Doliprane" and link this to the substance "Paracétamol", but also "femme enceinte" and link this to CIM-10 and Meddra (two different terminologies for pathologies) codes.

This API works pretty well, but as a lot of options and parameters, and is not well documented, and we'd like to provide an easier API directly in our brand new GraphQL portal.

We also have some other APIs that we'll integrate in this portal later.

**The goal of this challenge is to build a small backend service that will expose a GraphQL interface giving access to detected entities in a string.**

## Specifications

1. The challenge should be developped using NodeJS (with Javascript or Typescript)
2. You should use apollo-server to handle GraphQL-relative code
3. The NERL API can return several `entities`; entities are defined by the `entity` string that matched in the original text (with a corresponding `begin_offset` and `end_offset`), a `type` attribute and a `nel` list of linked objects in a terminology (with an `_id` code, a `name` and a similarity score). Here is a sample:

```json
{
  "query": "Doliprane chez la femme enceinte",
  "preprocessed_query": "doliprane chez la femme_enceinte",
  "entities": [
    {
      "begin_offset": 0,
      "end_offset": 10,
      "entity": "doliprane",
      "nel": [
        {
          "_id": "doliprane",
          "cosine_similarity": 1.0,
          "dci": [
            "paracétamol"
          ],
          "from_realm": true,
          "ingredients": [
            "paracétamol"
          ],
          "name": "doliprane",
          "type": "shortname",
          "countries": [
            "france",
            "algeria",
            "morocco",
            "tunisia"
          ]
        }
      ],
      "type": "DRUG"
    },
    {
      "begin_offset": 18,
      "end_offset": 32,
      "entity": "femme_enceinte",
      "nel": [
        {
          "_id": "10018708",
          "cosine_similarity": 1.0,
          "name": "Femme enceinte"
        }
      ],
      "type": "MEDDRA"
    },
    {
      "begin_offset": 18,
      "end_offset": 32,
      "entity": "femme_enceinte",
      "nel": [
        {
          "_id": "Z321",
          "cosine_similarity": 1.0,
          "name": "Grossesse confirmée"
        }
      ],
      "type": "DIAGNOSIS"
    }
  ]
}
```

4. The GraphQL schema should expose a GraphQL query taking the string we want to process as an argument and returning an object with `drugs` and `diagnosis` attributes. The former should return found `DRUG` entities (a flattened/unique'd list of values taken in `nel.*.dci`) and the later should return a list of `DIAGNOSIS` entities (all `_id` /  `name` pair).

5. The NERL API is accessible at URL `https://gloo.staging.posos.co/nerl/nerl?query=write_the_query_here` where you replace `write_the_query_here` by an URL encoded version of the string you want to process. This endpoint is protected by Google Cloud IAP, so you'll first need to get an authorization token. You've been given a Google Cloud service-account keyfile. You also have been given the IAP Client ID for the API. These, along the Google documentation, should be enough for you to authenticate and use the API.



## Tasks

1. Propose a GraphQL Schema providing the specified features - if you can't figure it out, ask us.
2. Make a small proof-of-concept for you to authenticate and use our NERL API. Ask us if you can't, we'll help you.
3. Propose an implementation for the API.
4. Ideally you send us a git repository with everything documented, a working Dockerfile and deployment instructions

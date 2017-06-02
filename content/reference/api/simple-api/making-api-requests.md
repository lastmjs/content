---
alias: koo4eevun4
path: /docs/reference/simple-api/making-api-requests
layout: REFERENCE
shorttitle: Making requests
description: Making requests to the Relay API can be done with Facebook's GraphQL client Relay or any other GraphQL client such as Apollo, Lokka or plain http.
simple_relay_twin: thaiph8ung
tags:
  - simple-api
  - lokka
  - apollo
  - plain-http
related:
  further:
    - eetai5meic
    - ol0yuoz6go
    - nia9nushae
    - eixu9osueb
  more:
    - cahzai2eur
    - ier7sa3eep
    - ligh7fmn38
    - uu2xighaef
---

# Sending GraphQL Requests

To actually send a request to the Simple API, you first need to copy the [endpoint](!alias-yahph3foch#project-endpoints) URL assigned to your project which looks like this:

`https://api.graph.cool/simple/v1/__PROJECT_ID__`


The Simple API is supposed to be consumed by GraphQL clients such as [Apollo Client](#apollo-client), or [lokka](#lokka) and can be also consumed with [plain HTTP requests](#curl).

## Apollo Client

### Setup

To set up the client with your endpoint:

```javascript
import ApolloClient, { createNetworkInterface } from 'apollo-client'

const client = new ApolloClient({
  networkInterface: createNetworkInterface({ uri: 'https://api.graph.cool/simple/v1/__PROJECT_ID__' }),
})
```

Now you can use Apollo to do queries and mutations. If you are unsure about the setup, check our Instagram example app using Apollo in [React](https://github.com/graphcool-examples/react-graphql/tree/master/quickstart-with-apollo) or [Angular 2](https://github.com/graphcool-examples/angular-graphql/tree/master/quickstart-with-apollo). Find more information in the official [documentation](http://dev.apollodata.com/).

## lokka

### Setup

To set up the client with your endpoint:

```javascript
const Lokka = require('lokka').Lokka;
const Transport = require('lokka-transport-http').Transport;

const client = new Lokka({
  transport: new Transport('https://api.graph.cool/simple/v1/__PROJECT_ID__')
});
```

Now you can use lokka to do queries and mutations. If you are unsure about the setup, check our [Todo example app using lokka](https://github.com/graphcool-examples/react-graphql/tree/master/quickstart-with-lokka-and-mobx). Find more information in the official [documentation](https://github.com/kadirahq/lokka/blob/master/README.md).

## HTTP and WebSockets

### Queries and Mutations

You can also communicate with the Simple API by using plain HTTP POST requests. For example, to query `allUsers`, do a POST request to your endpoint `https://api.graph.cool/simple/v1/__PROJECT_ID__`.

With `curl` you could do:

```
curl 'https://api.graph.cool/simple/v1/__PROJECT_ID__' -H 'content-type: application/json' --data-binary '{"query":"query {allUsers {id name}}"}' --compressed
```

With `fetch` you could do:

```
const response = await window.fetch('https://api.graph.cool/simple/v1/__PROJECT_ID__', {
    method: 'post',
    headers: {
        'content-type': 'application/json'
    },
    body: JSON.stringify({
        query: `
          query {
            allUsers {
              id
              name
            }
          }
        `
    })
});

const responseJSON = await response.json();
const data = responseJSON.data;
```

### Subscriptions

Subscriptions are managed through WebSockets. To establish a subscription, 

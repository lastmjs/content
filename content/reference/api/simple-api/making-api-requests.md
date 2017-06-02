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

```bash
curl 'https://api.graph.cool/simple/v1/__PROJECT_ID__' -H 'content-type: application/json' --data-binary '{"query":"query {allUsers {id name}}"}' --compressed
```

With `fetch` you could do:

```javascript
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

#### Establish connection

Subscriptions are managed through WebSockets. First establish a WebSocket connection:

```
let webSocket = new WebSocket('wss://subscriptions.graph.cool/v1/__PROJECT_ID__', 'graphql-subscriptions');
```
#### Initiate Handshake

Next you need to initiate a handshake with the WebSocket server. You do this by listening to the `open` event and then sending a JSON message to the server with the `type` property set to `init`:
```
webSocket.onopen = (event) => {
    const message = {
        type: 'init'
    };

    webSocket.send(JSON.stringify(message));
};
```

#### React to Messages

The server may respond with a variety of messages distinguished by their `type` property. You can react to each message as appropriate for your application:

```
webSocket.onmessage = (event) => {
    const data = JSON.parse(event.data);

    switch (data.type) {
        case 'init_success': {
            console.log('init_success, the handshake is complete');
            break;
        }
        case 'init_fail': {
            throw {
                message: 'init_fail returned from WebSocket server',
                data
            };
        }
        case 'subscription_data': {
            console.log('subscription data has been received', data);
            break;
        }
        case 'subscription_success': {
            console.log('subscription_success');
            break;
        }
        case 'subscription_fail': {
            throw {
                message: 'subscription_fail returned from WebSocket server',
                data
            };
        }
    }
};
```

#### Subscribe to Data Changes

To subscribe to data changes, send a message with the `type` property set to `subscription_start`:

```
const message = {
        id: '1',
        type: 'subscription_start',
        query: `
          subscription newPosts {
            Post(
              filter: {
                mutation_in: [CREATED]
              }
            ) {
              mutation
              node {
                description
                imageUrl
              }
            }
          }
        `
    };

    webSocket.send(JSON.stringify(message));
```

You should receive a message with `type` set to `subscription_success`. When data changes occur, you will receive messages with `type` set to `subscription_data`. The `id` property that you supply in the `subscription_start` message will appear on all `subscription_data` messages, allowing you to multiplex your WebSocket connection.

#### Unsubscribe from Data Changes

To unsubscribe from data changes, send a message with the `type` property set to `subscription_end`:

```
const message = {
    id: '1',
    type: 'subscription_end'
};

webSocket.send(JSON.stringify(message));
```

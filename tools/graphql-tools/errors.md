---
title: Error handling and logging
order: 308
description: Add better error logging to your GraphQL schema.
---

GraphQL servers can be tricky to debug. The following functions can help find error faster in many cases.

## API

<h3 id="forbidUndefinedInResolve" title="forbidUndefinedInResolve">
  forbidUndefinedInResolve(schema)
</h3>

ForbidUndefinedInResolve can be used during debugging to find mistakes in resolve functions faster. Usually, resolve functions only return undefined due to programmer error. `forbidUndefinedInResolve` takes a GraphQLSchema as input, and modifies it in place to throw an error when a resolve function returns undefined, telling you exactly which resolver returned undefined.
```js
import { forbidUndefinedInResolve } from 'graphql-tools';

forbidUndefinedInResolve(schema);
```


<h3 id="addErrorLoggingToSchema" title="addErrorLoggingToSchema">
  addErrorLoggingToSchema(schema, logger)
</h3>

This function may be deprecated in the near future. Instead of using addErrorLoggingToSchema, the `formatError` option of `apolloServer` or `graphqlHTTP` should be used, which was recently added in graphql-js v0.5.0

`addErrorLoggingToSchema` takes two arguments: `schema` and `logger`. `schema` must be an instance of `GraphQLSchema`, `logger` must be an Object with a callable property `log`. Every time an error occurs, `logger.log(e)` will be called.
```js
import { addErrorLoggingToSchema } from 'graphql-tools';
const logger = { log: (e) => console.error(e.stack) };
addErrorLoggingToSchema(mySchema, logger);
```
<h3 id="formatError" title="formatError">formatError</h3>

```js
import {graphqlKoa} from 'graphql-server-koa';
import Router from 'koa-router';
import schema from './my_schema.js';
import logger from './my_logger.js';

const router = new Router();
router.post('/graphql', graphqlKoa((ctx) => {
  return {
    // -> deactivate console.error in graphql-server
    // https://github.com/apollographql/graphql-server/blob/c05c1761c5a1a749482128604f607d2420b76be1/packages/graphql-server-core/src/runQuery.ts#L142
    debug: false, 
    schema,
    formatError: (err) => {
      logger.error(err); // -> my custom logger
      return new Error('Kaboom'); // -> Returns error to client
    }
  }
}));
```

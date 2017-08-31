---
title: Remote schemas and schema merging
order: 309
description: Operate on remote schemas and merge schemas together.
---

*NB* Those APIs are new in version 2.0 and are highly experimental. We plan to
change them based on the feedback from the community.

## API

<h3 id="makeRemoteExecutableSchema" title="makeRemoteExecutableSchema">
  addResolveFunctionsToSchema(fetcher)
</h3>

`makeRemoteExecutableSchema` takes a fetcher (eg [Apollo Link](https://github.com/apollographql/apollo-link))
and produces an
executable schema based on an existing GraphQL API that supports introspection.

<h3 id="mergeSchemas" title="mergeSchemas">
  mergeSchemas(options)
</h3>

Options are:

```js
{
  schemas: Array<GraphQLSchema>,
  links?: Array<{
    name: string;
    from: string;
    to: string;
    args?: Array<string>;
    fragment?: string;
    resolveArgs?: (
      source: any,
      args: { [argName: string]: any },
      context: any,
      info: GraphQLResolveInfo,
    ) => { [key: string]: any };
  }>,
  onTypeConflict?: (
    leftType: GraphQLCompositeType,
    rightType: GraphQLCompositeType,
  ) => GraphQLCompositeType;
}
```

Merge schema takes an array of schemas and merges them into one schema. It can
add additional fields to the schema through links (to connect schemas together).
If two types with the same name are encountered, `onTypeConflict` will be
called. If it's not provided, the error will be thrown.

#### Links

Link options:

* `name` - name of the field to add
* `from` - name of the type to add field to
* `to` - name of the root field to resolve the field with
* `args` - list of args that will be available as field arguments in the schema
* `fragment` - a GraphQL fragment with any fields that are needed to succesfully
   call `resolveArgs`
* `resolveArgs` - a function that should resolve in a plain object of arguments
   to be passed to root field

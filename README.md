# Light-house PHP

GraphQL is a query language for API. In laravel [Light House PHP (LHP)](https://lighthouse-php.com/) is a nice package for implement GraphQL in a convenient way. In this package, I try to explain main concepts of GraphQL and the LHP and its usge in Laravel.

## Types
Types are the reperesentation of entities like models:
```
type User {
    id: ID!
    name: String!
    mobile: String!
    created_at: DateTime!
    updated_at: DateTime!
}
```
## Schema and Query

Queries are some other types that have to be defined in schema files. We can define a query for find all items of a model like this:
```
type Query {
    users: [User!]! @all
}
```
The `@all` directive tells light-house to run a `select * from ...` query on the user type. Brackets mean array of items and the exclamination mark makes the fields `required` in the query.
with this query:
```
{
    users {
		name
    }
}
```
The result would be like this:
```
{
  "data": {
    "users": [
      {
        "name": "Shaun Crooks"
      },
      {
        "name": "Enola Gorczany"
      },
      ...
    ]
  }
}
```
If you want to get extra information about user you can fetch them according to the `User` type that we have defined:
```
{
    users {
		name
        mobile
        created_at
    }
}
```
### Pagination
If you want to paginate records, you can use `@paginate` decorator:
```
type Query {
    users: [User!]! @paginate
}
```
Now you can query data like this:
```
{
  users (first: 5, page: 1){
    data {
      id
      name
      created_at
    }
    paginatorInfo {
      currentPage
      lastPage
    }
  }
}
```
And the resulting output should be like this:
```
{
  "data": {
    "users": {
      "data": [
        {
          "id": "1",
          "name": "Shaun Crooks",
          "created_at": "2022-08-14 11:54:15"
        },
        ...
      ],
      "paginatorInfo": {
        "currentPage": 1,
        "lastPage": 3
      }
    }
  }
}
```
#### Find a record
In order to find a single item of a type simply use `@eq` and `@find` decorator:
```
type Query {
    user(id: ID! @eq): User @find
}
```
Now query it:
```
{
  user(id: 1) {
    name,
    mobile
  }
}
```
And the result:
```
{
  "data": {
    "user": {
      "name": "Shaun Crooks",
      "mobile": "+18642166300"
    }
  }
}
```
## Mutation
If you want to update or delete a record in DB you can use mutations in GraphQL.
In the following example I tried to create and update a user with `@create` and `@update` decorators.
```
type Mutation {
    createUser(name: String!, mobile: String!): User! @create
    updateUser(id: ID!, name: String): User @update
}
```
And finally for query this kind of API simply call the mutation from the client side:
```
mutation {
  createUser(name: "Ali", mobile: "0910***9283") {
    id
    name
    mobile
  }
}
```

This document will be updated in future and hope it help you.

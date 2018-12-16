# GraphQL

## Introduction

FaceBook has opened up some of their application development technologies among which notable are
1. React - UI technology 
2. GraphQL - API technology
3. Thrift - Client/Server protocol technology
4. Cassandra - NoSQL database technology based on One-Table-Per-Query

GraphQL is interesing way to define API.  Its an IDL (Interface Definition Language) for API server.  Its consumer would be a client UI over network. Other possibilities without an network are also possible.
GraphQL has 3 parts
1. Query
2. Mutation
3. Subscription

First step would be to define schema and a parallel can be drawn with relational database design. First step is defining entities to support business abstractions.  Database tables are classified into Transaction, Master and Reference (code) tables. In graphQL

```
type Application {
    id: ID!
    applicationtype: ApplicationType
    delaer: ID!
    applicants: [Applicant]!
}
```




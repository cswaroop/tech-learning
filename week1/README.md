# GraphQL

## Introduction

FaceBook has opened up some of their application development technologies among which notable are
1. React - UI technology 
2. GraphQL - API technology
3. Thrift - Client/Server protocol technology
4. Cassandra - NoSQL database technology based on One-Table-Per-Query

GraphQL is interesing way to define API.  Its an IDL (Interface Definition Language) for API server.  Its consumer would be a client UI over network. Other possibilities without any network are also possible.
GraphQL has 3 parts
1. Query
2. Mutation
3. Subscription

## SQL to GraphQL 

First step would be to define schema and a parallel can be drawn with relational database design. First step is defining entities to support business abstractions.  Database tables are classified into Transaction, Master and 
Reference (code) tables. In graphQL

```
-- Master table. Can be referenced into multiple transaction tables
CREATE TABLE dealer (
    id int NOT NULL PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    name text,
    description text    
);
-- Reference (lookup) table; nothing ever gets deleted
CREATE TABLE applicationtype (
    id int NOT NULL PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    name text,
    description text,
    isactive boolean
);
-- Transaction table; created/updated/deleted
CREATE TABLE application (
    id int NOT NULL PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    applicationtypeid int REFERENCES applicationtype (id),
    dealerid int NOT NULL REFERENCES dealer (id)
);
-- 1-Many table with `application` table
CREATE TABLE applicant (
    id int NOT NULL PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    applicationid int REFERENCES application (id),
    firstname text,
    middlename text,
    lastname text
);

```

A graphQL equivalent would be

```
enum ApplicationType {
    CONSUMER
    BUSINESS
}
type Dealer {
    id: ID!
    name: String
}
type Application {
    id: ID!
    applicationtype: ApplicationType
    dealerid: ID!
    applicants: [Applicant]!
}
type Applicant {
    id: ID!
    firstname: String
    middlename: String
    lastname: String
}
```




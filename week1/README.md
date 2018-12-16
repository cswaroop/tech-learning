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

First step would be to define schema and a parallel can be drawn with relational database design. First step is defining entities to support business abstractions.  Database tables are classified into Transaction, Master and Reference (code) tables.  The following schema typically represents  all the complexity we encounter in real-world applications.

```
-- Master table. Can be referenced into multiple transaction tables
CREATE TABLE product (
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
    productid int NOT NULL REFERENCES product (id)
);
-- An application can have many applicants 
-- Each applicant type can have one table. one-table-per-type inheritence
CREATE TABLE applicant (
    id int NOT NULL PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    applicationid int REFERENCES application (id),
    -- applicanttype can be PERSON or BUSINESS
    applicanttype text,
    position int
);
CREATE TABLE person_applicant (
    id int NOT NULL REFERENCES applicant (id),
    applicationid int REFERENCES application (id),
    firstname text,
    middlename text,
    lastname text
);
CREATE TABLE business_applicant (
    id int NOT NULL REFERENCES applicant (id),
    applicationid int REFERENCES application (id),
    tradingname text,
    legalname text,
    taxid text,
    employeecount numeric
);

CREATE TABLE bureaurecord (
    id int NOT NULL PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    pulledat timestamptz,
    request text,
    response text
);
-- A BureauRecord can be used by many applicants
-- An applicant can have many BureauRecords 
-- Its a Many-to-Many

CREATE TABLE applicant_bureaus (
    id int NOT NULL PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    applicationid int REFERENCES application (id),
    applicantid int REFERENCES applicant (id),   
    bureauid int REFERENCES bureaurecord (id)
);


```

A graphQL equivalent would be

```
enum ApplicationType {
    CONSUMER
    COMMERCIAL
}
type Product {
    id: ID!
    name: String
}
type Application {
    id: ID!
    applicationtype: ApplicationType
    productid: ID!
    applicants: [Applicant]!
}
enum ApplicantType {
    PERSON
    BUSINESS
}
interface Applicant {
    id: ID!
    applicantype: ApplicantType
    position: Int,
    bureaurecord: [BureauRecord]!
}
type PersonApplicant implements Applicant {
    id: ID!
    applicanttype: ApplicantType
    position: Int
    firstname: String
    middlename: String
    lastname: String
}
type BusinessApplicant implements Applicant {
    id: ID!
    applicanttype: ApplicantType
    position: Int
    tradingname: String
    legalname: String
    taxid: String
    employeecount: Int
}

type BureauRecord {
    id: ID!
    pulledat: AWSTimeStamp
    request: String
    response: String
}

```

These are abstractions closer to domain. They could be even more abstract like Collections, Graph, Edges and Nodes etc.   Yet you can see the impedence mismatch between GraphQL and SQL


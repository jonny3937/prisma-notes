# USING PRISMA WITH NODE.JS
Prisma is a porpular ORM(Object-Relational Mapping) toool that makes it easy to work with databases in node.js environment . Prisma helps developers save time and reduce the complexity of their code.

Prisma in matters of understanding one must know the following key areas
- setting up a prisma project
- Defining a data model
- Quering the database
- Interacting with CRUD (Create, Update & Delete).


## 1. Setting up a prisma project
This is the first step in using Pirsma with node.js .To do this you will need to install prisma to your command line interface. Which provides a convenient way to manage your Prisma projects and generate code.

 **NOTE**  You are required to install it as an NPM package.

To install prisma you are required to run this particular command in your terminal.

---bash
npm install prisma -D
---

and also

---bash
npm install prisma --save-dev
---
  Also we need to create a **.json** file in our project by running 
  ---bash 
  npm init -y
  ---

Next part is setting up the project .
Doing this you need to run a command and also give a name to your database

---bash
npx prisma init --datasource-provider Database
---

example.
npx prisma init --datasource-provider postgresql

The command above will create a .env file, the .env file is used in defining the environment variables, easy define of changes and also keeps sensitive data.
Also easy in matters of deployment.

## 2. Models in prisma
The data model definition part of the Prisma schema defines your application models (also called Prisma models). 

Models:
- Represent the entities of your application domain
- Map to the tables (relational databases like PostgreSQL)
- Form the foundation of the queries available in the generated Prisma Client 
- When used with TypeScript, Prisma Client provides generated type definitions for your models and any variations of them to make database access entirely type safe.

### models concept 
1. Field Types (Data Types)
In Prisma, field types define what kind of data each field can store. For your examples:
---prisma
model User {
  id        Int     @id @default(autoincrement())
  title     String?  // Optional string field
  name      String   // Required string field
  occupation String? // Optional string field
  age       Int?     // Optional number field
  isActive  Boolean @default(true) // Boolean with default
}
---
2. Data Models
These represent your application's entities/tables. The "normal data" would be the regular fields in your models:

---prisma
model Post {
  id        Int     @id @default(autoincrement())
  title     String
  content   String?
  published Boolean @default(false)
  createdAt DateTime @default(now())
}
---
3. Relations
Prisma supports different types of relationships between models:

---prisma
model User {
  id    Int    @id @default(autoincrement())
  posts Post[]  // One-to-many relation
}

model Post {
  id       Int  @id @default(autoincrement())
  author   User @relation(fields: [authorId], references: [id]) // Many-to-one
  authorId Int  // Relation scalar field
}
---
4. Attributes
These are special annotations that modify fields or models:

---prisma
model Product {
  id          Int     @id @default(autoincrement())
  name        String  @unique
  price       Float
  category    String  @map("product_category") // Maps to different column name
  createdAt   DateTime @default(now()) @updatedAt
  isFeatured  Boolean @default(false)
  
  @@index([name]) // Creates an index
  @@unique([name, price]) // Composite unique constraint
}
---
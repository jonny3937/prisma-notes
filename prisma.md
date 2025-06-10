# USING PRISMA WITH NODE.JS
Prisma is a porpular ORM(Object-Relational Mapping) toool that makes it easy to work with databases in node.js environment . Prisma helps developers save time and reduce the complexity of their code.

Prisma in matters of understanding one must know the following key areas
- setting up a prisma project
- Defining a data model
- Interacting with CRUD (Create, Update & Delete).


## 1. Setting up a prisma project
This is the first step in using Pirsma with node.js .To do this you will need to install prisma to your command line interface. Which provides a convenient way to manage your Prisma projects and generate code.

 **NOTE**  You are required to install it as an NPM package.

To install prisma you are required to run this particular command in your terminal.

```bash
npm install prisma -D
```

and also

```bash
npm install prisma --save-dev
```
  Also we need to create a **.json** file in our project by running 
```bash 
  npm init -y
  ```

Next part is setting up the project .
Doing this you need to run a command and also give a name to your database

```bash
npx prisma init --datasource-provider Database
```

example.
npx prisma init --datasource-provider postgresql

The command above will create a .env file, the .env file is used in defining the environment variables, easy define of changes and also keeps sensitive data.
Also easy in matters of deployment.

Remember to visit .env file and set username and password.

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

```js
model User {
  id        Int     @id @default(autoincrement())
  title     String?  // Optional string field
  name      String   // Required string field
  occupation String? // Optional string field
  age       Int?     // Optional number field
  isActive  Boolean @default(true) // Boolean with default
}
```
2. Data Models


These represent your application's entities/tables. The "normal data" would be the regular fields in your models:

```js
model Post {
  id        Int     @id @default(autoincrement())
  title     String
  content   String?
  published Boolean @default(false)
  createdAt DateTime @default(now())
}
```
3. Relations


Prisma supports different types of relationships between models:

There are ;

One-to-One Relationship
Definition: A single record in one model relates to exactly one record in another model

Example: A User has exactly one Profile, and a Profile belongs to exactly one User

Prisma Characteristic: Uses @unique constraint on the relation field

2. One-to-Many Relationship
Definition: A single record in one model can relate to multiple records in another model

Example: A User can have many Posts, but each Post belongs to only one User

Prisma Characteristic: Uses array syntax (Post[]) on the "one" side

3. Many-to-Many Relationship
Definition: Multiple records in one model relate to multiple records in another model

Example: A Post can have many Tags, and a Tag can be on many Posts

Prisma Characteristic: Can be implicit (automatic join table) or explicit (custom join table model)

4. Many-to-One Relationship
Definition: Essentially the "many" side of a one-to-many relationship

Example: Many Posts belong to one User (same as one-to-many but viewed from the Post perspective)

Prisma Characteristic: Uses a relation scalar field (like authorId) with @relation attribute

```js
model User {
  id    Int    @id @default(autoincrement())
  posts Post[]  // One-to-many relation
}

model Post {
  id       Int  @id @default(autoincrement())
  author   User @relation(fields: [authorId], references: [id]) // Many-to-one
  authorId Int  // Relation scalar field
}
```
4. Attributes
These are special annotations that modify fields or models:

```js
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
```

## migration
Prisma Migrate is a powerful database migration tool that integrates with the Prisma schema for data modeling. It allows you to manage your database schema changes in a deterministic and repeatable manner.

Example

To create a new migration, you can use the following command:
```bash
npx prisma migrate dev --name init
```
This command will generate a new migration file based on the changes in your Prisma schema.
Remember that if a single change is made of a file one must run migtration to update changes.


### Prisma Client

Prisma Client JS is an auto-generated query builder that enables type-safe database access and reduces boilerplate.

it's generated based on the models you define in your prisma schema .

Client generation command
```bash
npx prisma generate
```
The command above reads schema and generates prismaclient inside the node module.


 @packages/client should be downloaded outomatically if not one should run the below command
 ```bash
 npm install @prisma/client
 ```

 ### CRUD operations in prisma
 CRUD stands for Create, Read, Update, and Delete - the four basic operations for persistent storage.
 
  Here's how these operations work in Prisma:

1. Create (C)
Used to add new records to your database.

```typescript
const newUser = await prisma.user.create({
  data: {
    name: 'Alice',
    email: 'alice@example.com'
  }
});
```

Create multiple records
```typescript
const newUsers = await prisma.user.createMany({
  data: [
    { name: 'Bob', email: 'bob@example.com' },
    { name: 'Charlie', email: 'charlie@example.com' }
  ]
});

```
2. Read (R)
Used to fetch data from your database.

```typescript

const user = await prisma.user.findUnique({
  where: { id: 1 }
});


```

Find many records

```typescript
const users = await prisma.user.findMany();
```

With filtering

```typescript

const activeUsers = await prisma.user.findMany({
  where: { isActive: true }
});
```
With relation loading


```typescript
const usersWithPosts = await prisma.user.findMany({
  include: { posts: true }
});

```
3. Update (U)
Used to modify existing records.

```typescript
const updatedUser = await prisma.user.update({
  where: { id: 1 },
  data: { name: 'Updated Name' }
});
```
Update many records

```typescript
const updatedUsers = await prisma.user.updateMany({
  where: { isActive: false },
  data: { status: 'INACTIVE' }
});
```

4. Delete (D)
Used to remove records from your database.


```typescript
const deletedUser = await prisma.user.delete({
  where: { id: 1 }
});
```
Delete many records

```typescript
const deletedUsers = await prisma.user.deleteMany({
  where: { isActive: false }
});
```

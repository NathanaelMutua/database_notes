# Prism ORM

This is a tool that gives developers the ability to interact with databases using an OOP point of view, instead of using queries like in SQL.

It's an Object-relational mapping library for Node.js and Typescript.

---

# Table of contents

1. [Benefits of using Prisma ORM](#benefits-of-using-prisma-orm)
2. [Installation](#installation-of-prisma)
3. [Set Up](#setup-of-prisma)
4. [Models](#models)
5. [Migrations](#migrations)
6. [CRUD Operations in an ORM](#crud-operations-in-prisma-orm)
7. [Relationships](#relationships)

---

## Benefits of using Prisma ORM

- Improves application performance by implementing high performance engine for database.
- Easy to learn and requires minimum setup.
- It provides schema migration.
- Provides database support.
- Prevents SQL injection.
- Makes switching databases easier.

---

## Installation of Prisma

The basic steps of installing prisma ORM to your designated folder.

### Step 1: Installing Prisma

Before we install the npm package for prisma, we need to install it as a dependency in our project.

Initializing the ```package.json``` file in the project:

```bash
npm init -y
```

Then we can now install the package:
Globally:

```bash
npm install prisma --save
```

As a development dependency:

```bash
npm install prisma --save-dev
```

or

```bash
npm install prisma -D
```

### Initializing a Prisma project

We run the command as follows

```bash
npx prisma init
```

To see other commands at your disposal you can just check, by running the command:

```bash
npx prisma
```

---

## Setup of Prisma

After installation we need to carry out setting up the Prisma project to operate with our current database and configurations.

### Database setting

To do this, we run the command:

```bash
npx prisma init --datasource-provider postgresql
```

If it is another database, we can replace it where ```postgresql``` appears in the command with the appropriate database.

Noted that after running the command above we got a new folder in the ```node_modules``` directory, ```prisma``` which contained the following files:
    - .env
    - prisma.schema

### Configuring database connection

 In the ```.env``` file we will configure the database connection by changing or adding the foolloeing details:
    - User
    - Password
    - Host
    - Port
    - Database

What it looks like initially:

```bash
DATABASE_URL="postgresql://johndoe:randompassword@localhost:5432/mydb?schema=public"
```

The details should be the ones that I have for my database.

Particularly for me, I am using postgresql.

```bash
DATABASE_URL="postgresql://postgres:abcd12@localhost:5432/employees"
```

So to break this down:

|       BEFORE        |        AFTER        |
|---------------------|---------------------|
|  johndoe            |     postgres        |
|  randompassword     |     abcd12          |
|  5432               |      5432           |
|  @localhost         |     @localhost      |
|  mydb               |     employees       |

> The items that remain unchanged are the default ones, such as the port ```5432``` and local host, which I did not change in my setup.

---

## Models

Models are what we call tables in a regular database.

These models represent or map to the tables in postgresql or collections.

**Example** of a model:

```sql
model User{
  id              String   @id @default(uuid())
  firstName       String   @map("first_name")
  lastName        String   @map("last_name")
  age             Float ?   @map("age")
  userEmail       Int      @map("user_email") @unique

  @@map("users_table")}
```

### Concepts of the Prisma database Model

- Data Models: these define the tables in the schema
- Field Types: are like the data types for each column
- Relations: these tell us how models/tables relate/ are connected with each other.
- Attributes: these, using the ```@``` symbol, add additional metadata to fields.

### Fields in the Prisma Database Model

As seen above, fields are like the entries for the tables. But let's not confuse them with a 'record', which is a row of a table in a database.

#### Parts of a Field

1. Field Name
2. Datatype of the field. (string, int, bigint, float, decimal, boolean, bytes, JSON and UUID)
3. Field type modifier; are '?' for optional, and '[]' for multiple values.
4. Attributes. (@id, @unique, @default, @map, @relation, @@index, @@unique, @db, @length, )

## Migrations

Migrations in prisma are a systematic way to transform the database schema over time in a version-control-like manner.

### Purpose of migrations

- Version Control for database schema
- Team collaboration
- Deployment automation

### How migrations work

#### Define schema changes

First we need to modify our prisma schema file ```schema.prisma``` to now define the state of our database schema.

Here we can be adding new models or changing details in the schema.

#### Generate migration files

Here, we now generate a new migration by running a Prisma command line command:

```bash
npx prisma migrate dev
```

This generates an SQL migration file that represents the current state of the schema.

It is usually always good practice to name each migration. So the command to do that would look like this:

```bash
npx prisma migrate dev --name migration-name
```

```migration-name``` is the name that you replace with the correct name.

### NOTE: Prisma Client

**Prisma Client** is a type-safe autogenerated query builder that composes the logic of the prisma schema and forms. It is usually auto created during migration, but if it doesn't we run the command ```npm install @prisma/client``` to install the Client

**Importing the Prisma Client** in JS, it usually has the syntax below:

```js
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient()
```

**Sending Queries** to the database using the Prisma Client:

```js
// run inside `async` function
const newUser = await prisma.user.create({
  data: {
    name: 'Alice',
    email: 'alice@prisma.io',
  },
})
const users = await prisma.user.findMany()
```

## CRUD operations in Prisma ORM

### Creating records

This involves creating new records.

We use the following commands:

- ```create()```
- ```createMany()``` this returns an object with a count field

#### Examples for creating records

Using ```create()```:

```js
const user = await prisma.user.create({
  data: {
    email: 'rizman@yahoo.com',
    name: 'Samuel Gishu',
  },
})
```

Using ```createMany()```:

```js
const createMany = await prisma.user.createMany({
  data: [
    { name: 'Bob', email: 'bob@gmail.com' },
    { name: 'Boblin', email: 'bob@yahoo.com' },
    { name: 'Yesh', email: 'yesh@msn.net' },
    { name: 'Angel', email: 'angel@gmail.com' },
  ],
})
```

### Reading records

This is the process of viewing the information stored in the database.

We use the following methods:

- ```findMany()``` is a query that returns all user records
  
  ```js
  const users = await prisma.user.findMany()
  ```

- ```findFirst()``` Returns the first record that meets the condition.

  ```js
  const findUser = await prisma.user.findFirst({
    where: {
      posts: {
        some: {
          likes: {
            gt: 100,
          },
        },
      },
    },
    orderBy: {
      id: 'desc', // just realized you can also add a sorting technique
    },
  })
  ```

- ```findUnique()``` Returns a single record by a unique identifier or id.

### Updating Records

This refers to changing the details of a particular record.

We use the ```update()``` method:

```js
const updateUser = await prisma.user.update({
  where: {
    email: 'rizman@yahoo.com',
  },
  data: {
    name: 'Samuel Gishu Rizman',
  },
})
```

or the  ```updateMany()``` method:

```js
const updateUsers = await prisma.user.updateMany({
  where: {
    email: {
      contains: 'yahoo.com',
    },
  },
  data: {
    department: 'Engineering',
  },
})
```

### Deleting Records

This involves permanently getting rid of a record from a model/table.

We use the following methods:

- ```delete()```

  ```js
  const deleteUser = await prisma.user.delete({
    where: {
      email: 'bert@prisma.io',
    },
  })
  ```

- ```deleteMany()```
  
  ```js
  const deleteUsers = await prisma.user.deleteMany({
    where: {
      email: {
        contains: 'prisma.io',
      },
    },
  })
  ```

## Relationships

In Prisma we use the term ```relation```, which is a connection between two models in the Prisma schema.

### One-to-many relationship

Usually refers to a scenario where one record on one side of the relationship can be connected to zero or more records on the other table.

**Example** right below:

```js
model User {
  id    Int    @id @default(autoincrement())
  posts Post[]
}

model Post {
  id       Int  @id @default(autoincrement())
  authorId Int
  title String
  author   User @relation(fields: [authorId], references: [id])
}
```

### One-to-many Relationship

This refers to a scenario where one record can be connected to zero or more records on the other table.

```js
model User {
id Int @id @default(autoincrement())
posts Post[]
}

model Post {
id Int @id @default(autoincrement())
author User @relation(fields: [authorId], references: [id])
authorId Int
}
```

I enjoyed learning, if you want more materials, try the ones below🚀

## References

- [Prisma Documentation](https://www.prisma.io/docs/orm/prisma-client)
- [Notes on Prisma](https://prisma-basics-notes.vercel.app/migrations.html)
- [Medium Post](https://medium.com/@moiserushanika2006/exploring-prisma-advantages-and-drawbacks-with-code-examples-5c9af798cc0d)
- [Medium post](https://medium.com/@SahilSharma_SoftwareDeveloper/migrations-in-prisma-0a2f4e660e14)
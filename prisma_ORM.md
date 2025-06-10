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
6. [Prisma Clients]
7. [CRUD Operations in an ORM]
8. [Relationships]

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

Initializing the ```packaje.json``` file in the project:

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

Migrations in prisma are a systematic way to transform the database schema over time in a version-control-like manner
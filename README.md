[![General Assembly Logo](https://camo.githubusercontent.com/1a91b05b8f4d44b5bbfb83abac2b0996d8e26c92/687474703a2f2f692e696d6775722e636f6d2f6b6538555354712e706e67)](https://generalassemb.ly/education/web-development-immersive)

# PeeWee

[PeeWee](http://docs.peewee-orm.com/en/latest/) is a simple and small ORM, much
like Mongoose, but for SQL databases.
[There are many others like it](https://docs.python-guide.org/scenarios/db/),
but we're focusing on PeeWee because it's designed to be similar to Django's
ORM.

## Prerequisites

- MongoDB and Mongoose
- SQL
- Python

## Objectives

By the end of this, developers should be able to:

- Setup PeeWee and use it to connect to a SQL database
- Define PeeWee models
- Query a SQL database with a PeeWee model

## Introduction

PeeWee is an Object-Relational Mapper, very similar to Mongoose (the
Object-Document Mapper we used with Express).

There are three key things we'll use PeeWee for:

1. Connecting to a PSQL database from within a Python program
1. Modeling our data
1. Querying out database through out models

## I Do: Quickstart

From the `root directory` cd into the `lib` dir and run 

`pipenv install peewee psycopg2 autopep8`. 


**Be sure to be inside if the `lib` directory**

Go ahead and start your virtaual environment with the command:
`pipenv shell`

I'll walk through a quick setup of PeeWee, showing you how to connect to a
database, create a model, and then do a query.

### Connecting to the Database

Once we've installed PeeWee, we need to install the Python PostgreSQL driver
(`psycopg2`). Both are already in the [`Pipfile`](./lib/Pipfile) inside the of
`lib/`.

Once everything is installed, we need to import PeeWee and set up our database
connection. This should feel pretty similar to how we did it with Mongoose.

```py
from peewee import *

db = PostgresqlDatabase('people', user='postgres', password='',
                        host='localhost', port=5432)

db.connect()
```

In the above snippet of code we are:

1. Importing `peewee`
1. Using the `PostgresqlDatabase` class to create a database connection, passing
   in the name of the database, the user, the password, the host, and the port.
1. Use `db.connect()` to actually connect to the database

### Defining a Model

In PeeWee (and most Python ORMS), we use a class to define our models. PeeWee
gives us a base `Model` class that we can inherit from. Our model needs to
define the fields for that SQL table as well as the database the table should be
in (because there can be more than one).

We're going to start by defining a `BaseModel` class that sets the database
connection:

```py
class BaseModel(Model):
    class Meta:
        database = db
```

A `Meta` class is a class that describes and configures another class.

Now that we have our `BaseModel`, we can define our model and have it inherit
from this `BaseModel` class:

```py
class Person(BaseModel):
    name = CharField()
    birthday = DateField()
```

`CharField()` and `DateField()` are from
[PeeWee's field's](http://docs.peewee-orm.com/en/latest/peewee/models.html).

Once we have the model, we need to add the corresponding table to the database:

```py
db.create_tables([Person])
```

### Querying our Model

The first query we'll perform is to create some records in our database. PeeWee
makes this easy to do:

```py
zakk = Person(name='Zakk', birthday=date(1990, 11, 18))
zakk.save()
```

Now go and check you `people` database in postgres - you should see a record
with a name of `'Zakk'` and a birthday of `1990-11-18`.

## You Do: Define a Pet model

Define a `Pet` model with the following properties:

- `name`, should be a `CharField()`
- `animal_type`, should be a `CharField()`

Once you've defined your model, create at least 5 instances. Be sure to call
`.save()`!

## Reading Data

We use the `.get()` method to find a single record, otherwise we use `select()`.
Here are a few examples:

```py
Person.get(Person.name == 'Zakk')
Person.get(Person.birthday == date(1990, 11, 18))
Person.select()
Person.select().where(Person.birthday < date(1990, 1, 1))
```

## You Do: Reading Data

Use your `Pet` model to read some data from your `pets` table. Use both `.get()`
and `.select()`.

## Updating Data

The easiest way to update a record in the database is to change the value of a
property and then call `.save()` again:

```py
zakk = Person.get(Person.name == 'Zakk')
zakk.birthday = date(1980, 11, 18)
zakk.save()
```

## Deleting Data

Finally, to delete data, we use the `.delete_instance()` method:

```py
zakk.delete_instance()
```

## Additional Resources

- [PeeWee Documentation](http://docs.peewee-orm.com/en/latest/)
- [PeeWee official example app](http://docs.peewee-orm.com/en/latest/peewee/example.html)
- [Bonus: Relationships and Joins](http://docs.peewee-orm.com/en/latest/peewee/relationships.html)

## [License](LICENSE)

1. All content is licensed under a CC­BY­NC­SA 4.0 license.
1. All software code is licensed under GNU GPLv3. For commercial use or
   alternative licensing, please contact legal@ga.co.

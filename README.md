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
1. Querying our database through our models

## I Do: Quickstart

1. Fork and Clone this repo
2. `cd` into the `lib` dir
3. Run `pipenv install peewee psycopg2-binary autopep8`
   * This command will create a `Pipfile` and `Pipfile.lock` file inside the lib folder; you should see all your `packages` listed inside the `Pipfile` file.
4. Start your virtual environment: `pipenv shell`
    * Your terminal should change from something like this:
        ```bash
            Rogers-MBP-2:lib roger$
        ```
    * To something like this:
        ```bash
            (lib) bash-3.2$
        ```

I'll walk through a quick setup of PeeWee, showing you how to connect to a
database, create a model, and then do a query.

### Create a Database in psql

In order to successfully run the code we are going to create, you must create
a Database in `psql`. This is something you are able to do via `Peewee` but for
now we will create a Database through the shell.

Steps:
1. Open a separate terminal window from your virtual environment.
2. Enter the `psql` shell by typing: `psql` (you may have to run `psql postgres` if you get a "database does not exist" error)
3. Create a `Database`: `CREATE DATABASE people;`

### Connecting to the Database

Once we've installed PeeWee, we need to install the Python PostgreSQL driver
(`psycopg2`). Both are already in the [`Pipfile`](./lib/Pipfile) inside the of
`lib/`.

Once everything is installed, we need to import PeeWee and set up our database
connection. This should feel pretty similar to how we did it with Mongoose.

```python
from peewee import *

db = PostgresqlDatabase('people', user='postgres', password='',
                        host='localhost', port=5432)

db.connect()
```

> Note that your database settings may differ

In the above snippet of code we are:

1. Importing `peewee`
1. Using the `PostgreqlDatabase` class to create a database connection, passing
   in the name of the database, the user, the password, the host, and the port.
1. Use `db.connect()` to actually connect to the database

### Defining a Model

In PeeWee (and most Python ORMS), we use a class to define our models. PeeWee
gives us a base `Model` class that we can inherit from. Our model needs to
define the fields for that SQL table as well as the database the table should be
in (because there can be more than one).

We're going to start by defining a `BaseModel` class that sets the database
connection:

```python
class BaseModel(Model):
    class Meta:
        database = db
```

A `Meta` class is a class that describes and configures another class. More on `Meta` classes [here](https://blog.ionelmc.ro/2015/02/09/understanding-python-metaclasses/)

Now that we have our `BaseModel`, we can define our model and have it inherit from this `BaseModel` class:

```python
class Person(BaseModel):
    name = CharField()
    birthday = DateField()
```

`CharField()` and `DateField()` are from
[PeeWee's field's](http://docs.peewee-orm.com/en/latest/peewee/models.html).

Once we have the model, we need to add the corresponding table to the database:

```python
db.create_tables([Person])
```

### Querying our Model

The first query we'll perform is to create some records in our database. PeeWee
makes this easy to do:

```python
zakk = Person(name='Zakk', birthday=date(1990, 11, 18))
zakk.save()
```

Now go and check you `people` database in postgres - you should see a record
with a name of `'Zakk'` and a birthday of `1990-11-18`.

### Check Your Work

Now to check our work we need to do the following:

1. Be sure you are in your `Virtual Environment`;
   * If you are unsure how to do this check the earlier section.
2. Run the command: `python3 main.py`
   * This will execute the program you have created in `main.py`; i.e. It runs your code.
3. In a **separate terminal** access `psql`
4. Connect to your `people` database using: `\c people`
5. Select all records in the `Person` table: `SELCET * FROM Person`
    * You should see the following output:
    ```
        id | name | birthday
        ----+------+------------
        1 | Zakk | 1990-11-18
        (1 row)
    ```

## You Do: Define a Pet model

Define a `Pet` model with the following properties:

- `name`, should be a `CharField()`
- `animal_type`, should be a `CharField()`

Once you've defined your model, create at least 5 instances. Be sure to call `.save()`!

<details>
<summary>Solution</summary>

```python
class Pet(BaseModel):
name = CharField()
animal_type = CharField()

db.drop_tables([Pet])
db.create_tables([Pet])

velvet = Pet(name='Velvet', animal_type='Dog')
velvet.save()

chip = Pet(name='Chip', animal_type='Dog')
chip.save()

spot = Pet(name='Spot', animal_type='Cat')
spot.save()

groot = Pet(name='Groot', animal_type='Treeanoid')
groot.save()

shelly = Pet(name='Shelly', animal_type='Turtle')
shelly.save()
```
</details>


## Reading Data

We use the `.get()` method to find a single record, otherwise we use `select()`.
Here are a few examples:

```python
Person.get(Person.name == 'Zakk')
Person.get(Person.birthday == date(1990, 11, 18))
Person.select()
Person.select().where(Person.birthday < date(1990, 1, 1))
```

To have this data print in the terminal you could do something like this:

```python
grabbing_zakk = Person.get(Person.name == 'Zakk')
print(grabbing_zakk.birthday)
# output: 'Zakk'

list_of_people = Person.select().where(Person.birthday > date(1990, 1, 1))
print([user.birthday for user in list_of_people])
# output: [datetime.date(1990, 11, 18), datetime.date(1990, 5, 27)]
```

## You Do: Reading Data

Use your `Pet` model to read some data from your `pets` table. Use both `.get()` and `.select()`.


<details>
<summary>Solution</summary>

```python

find_groot = Pet.get(Pet.name == 'Groot')
print(find_groot)
# Outputs: 1; which is the records ID
print(find_groot.name)
# Outputs: Groot
print(find_groot.animal_type)
# Outputs: Treeanoid


list_of_pets = Pet.select().where(Pet.animal_type == 'Dog')
print([pet.name for pet in list_of_pets])
# Outputs: ['Velvet', 'Chip']
```
</details>


## Updating Data

The easiest way to update a record in the database is to change the value of a
property and then call `.save()` again:

```python
zakk = Person.get(Person.name == 'Zakk')
zakk.birthday = date(1980, 11, 18)
zakk.save()
```

## Deleting Data

Finally, to delete data, we use the `.delete_instance()` method:

```python
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

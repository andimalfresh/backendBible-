This repo is for referencing the basic steps to set up a Database using PostgreSQL and Knex

//changetest

// This is the Backend bible (for knex: created by Jimmy Liang, sequelize to be added later) 

*App initialization:
    npm init
    git init
    touch app.js

*Express
    npm install express

Setup Express with your app.js

*Knex and PostgresQL:

    npm install knex pg
    npm install -g knex
    createdb <db_name>
    knex init

Open knexfile.js that was created and remove the staging section
Change development environment and production to clients "pg"

ie::

    development: {
      client: 'pg',
      connection: 'postres://postgres:winter@localhost/<DB_NAME>'
    // - (( connection: 'localhost/<DB_NAME>'))
    },

    production: {
      client: 'pg',
      connection: process.env.DATABASE_URL
    }
  };

*MIGRATIONS

knex migrate:make students(migration_name)

exports.up = knex migrate:latest
exports.down = knex migrate:rollback

    exports.up = function(knex, Promise) {
        return knex.schema.createTable("orders", (orders) => {
          orders.increments("id")
          orders.string("date")
          orders.string("time")
          orders.string("delivery_date")
          orders.string("delivery_window")
          orders.string("productsTablejoin")
          orders.integer("cust_acct").references("id").inTable("users").onDelete("CASCADE")
          orders.integer("dist_acct").references("id").inTable("users").onDelete("CASCADE")
        }) 
      }
      
      exports.down = function(knex, Promise) {
        return knex.schema.dropTableIfExists("orders")
      }
  

  *SEEDS 

    knex seed:make 01_students

    exports.seed = function(knex, Promise) {
      // Deletes ALL existing entries
      return knex('orders').del()
        .then(function () {
          // Inserts seed entries
          return knex('orders').insert([
            {
              date: "11-19-1449",
              time: "1855",
              delivery_date: "08-85-1200",
              delivery_window: "0400",
              productsTablejoin: "This will be where we join the products from the table" ,
              dist_acct: 1,
              cust_acct: 2,
            }
          ]);
        });
    };

knex seed:run

*DATABASE CONNECTION

Create a file called database-connection.js containing the following:

// Set environment 

const environment = process.env.NODE_ENV || 'development'

// Use connectiono info from knexfile

const configEnv = config[environment]

// Bring in knex as a library 

const knex = require('knex')

// Put it all together

const connection = knex(configEnv)

// Export so we can use it in other routes
module.exports = connection

*Queries 

Create a file called queries.js containing the following: 

const db = require("./database-connection")

    module.exports = {
        listAll() {
            return db("orders")
        },
        createOrder(createOrder) {
            return db("orders")
            .insert(createOrder)
            .returning("*")
        },
        deleteOrder(id) {
            return db("orders")
            .where("id",id)
            .delete()
        },
        getById(id) {
            return db("orders").where("id", id)
        },
        updateOrder(id, orders) {
            return db("orders")
              .where('id', id)
              .update(orders)
              .returning('*')
          }
    }
 
 Add queries.js as a dependency for app.js 

 const queries = require('./queries')

*HEROKU 

    heroku login 
    heroku create <db_name>
    git remote -v
    heroku addons:create heroku-postgresql:hobby-dev
    heroku run knex migrate:latest
    heroku run knex seed:run

// -Fin.
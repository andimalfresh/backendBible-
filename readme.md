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
    connection: 'postres://postgres:winter@localhost/uumbrella'
  },

  production: {
    client: 'pg',
    connection: process.env.DATABASE_URL
  }

};


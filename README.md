# Run a local postgres database container to connect with Springboot Application

 In this example, we will run a Springboot app which will connect with a local database running in docker container.
 Additionally, we will see Flyway script in action to create a table in the database.
 Note that Spring expects that the database to which we wish to connect is already created. Morever, we must use the
 username exactly same as database name. Otherwise, flyway wont work and wont find database even if it exists.
 
## Steps to run the application

Firstly, lets spin up a docker container for postgres to connect our Springboot app with it.

1. Pull a docker container for postgres: `docker pull postgres:apline3.18`
2. Run postgres docker container to start postgres server: `docker run --rm --name postgres_0 -e POSTGRES_PASSWORD=password -d -p 5432:5432 docker.io/library/postgres:alpine3.18`
3. See that postgres container is still running in detached mode: `docker ps`
4. Connect to postgres server container: `docker exec -it postgres_0 bash`
5. After tunneling inside, type this command to connect to default postgres database and postgres user provided out of box by Postgresql: `psql -d postgres -U postgres -W` and enter password as `password`
6. Now, type this to list all databases in our container: `\l`
7. Observe that we have a postgres database already with postgres user. This is the default database and user provided by Postgresql out of box. 
8. Now, create a new role. This role and the database name which we will create in next step need to match in order to connect it to our Springboot app: `CREATE ROLE test WITH LOGIN PASSWORD 'password';`
9. Type this to see all users and roles and their privileges: `\du`
10. Now, alter role to assign it with createdb privilege: `ALTER ROLE test CREATEDB;`
11. Type this to see all users and roles and their privileges: `\du`
12. Now, type: `\q` to quit postgres server
13. Now, login with test user: `psql -d postgres -U test -W` and enter password as `password` 
9. Now, lets create a database with same name as our user, i.e. test: `CREATE DATABASE test;` 
10. Type this to see all databases in our container: `\l`
11. Now, connect to test database using: `\c test` or '\connect test'
12. Type: `\dt` to see all tables in test database. Observe that there are no tables (or relations) in test database right now.

Now, lets run out Springboot app:

1. Go to the root directory of this project and run: `mvn clean install`
2. Run the application using command: `mvn spring-boot:run`
3. Observe that the application is running on port 8080
4. Observe that Flyway has created a table in test database in our container. 
   Type: `\dt` to see all tables in test database. Observe that there is a table named `student` in test database now.
   Type this to see records inside student table: `select * from student;`
   This table and records are created by Flyway from script under `src/main/resources/db/migration` directory.

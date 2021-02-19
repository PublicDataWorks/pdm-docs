---
layout: default
title: FAQ
parent: Technical Content
---


# FAQ


## Useful Tools

Q. What tools will I need or will be useful for this project?

A. Necessary tools:

-   Docker: containerization. Follow the README for instructions on downloading and running Docker. If you choose to sign in (not a requirement), sign in under you username not your email address. Docker-compose allows us to chain and run multiple containers. **Note that anytime you make a change to package.json, you will have to rebuild using ./scripts/docker-compose-build.sh from the root folder.** If you want to clear out or local database or suspect severe issues with the container itself, run docker-compose down to completely destroy the containers.
-   Postgres: SQL database for PDM. Will be automatically created, started, and migrated via Docker.
-   Git: Automatically pre-installed with Macs. Windows users will need to download it.
-   Yarn: Manages our packages. You may need to install it in order to run end-to-end tests, but otherwise, Docker will handle installing yarn packages.


### Nice to haves:

-   pgAdmin: Great tool for looking inside your Postgres database.

To connect to the Police Data Manager local database:

    - Right click Server.
    - Select Create > Servers.
    - In the new modal, name the server anything you want. 
    - In the Connections tab: set "Host name/address" to "localhost"; "Maintenance database" should be set to "noipm-db" and "Username" should already be set to "postgres"; set "Password" to "password".
    - Save.

-   JSON View Chrome extension: Makes any JSON pages (including feature toggles) pretty. Download the Chrome extension here: <https://chrome.google.com/webstore/detail/json-viewer/gbmdgpbipfallnflgajpaliibnhdgobh?hl=en-US>


## Local Environment

Q. Which environment variables will I need?

A. Local machine environment (`.bashrc`, `.zshrc`, etc.):
`REACT_APP_GOOGLE_API_KEY`: This key should be located in your `.profile`, `.zshrc`, or comparable local machine environment. It allows you to access address data from Google Maps.

Q. I'm getting this error when I am on the website: `Failed to load resource: net::ERR_CERT_AUTHORITY_INVALID`

A. On Chrome, go to `chrome://flags/#allow-insecure-localhost` and enable the flag for "Allow invalid certificates for resources loaded from localhost.". Alternatively, on the latest Chrome, you can type `thisisunsafe` into the error page and bypass that error temporarily.

Q. I'm getting this error when trying to run up or down migrations: `getaddrinfo ENOTFOUND db db:5432`

A. Using your text editor of choice, edit `/etc/hosts` file to make it look like the following: 

    ##
    # Host Database
    #
    # localhost is used to configure the loopback interface
    # when the system is booting.  Do not change this entry.
    ##
    127.0.0.1       localhost
    127.0.0.1       db
    127.0.0.1       redis
    255.255.255.255 broadcasthost
    ::1             localhost

The reason that you need to edit this file is so that your computer knows what address to go to when the migrations use db or redis.

Q. I'm getting this error when trying to re-build the docker containers: `ERROR: Service '[...]' failed to build: [...] : no space left on device`.

A. Run Docker Prune using the command `docker system prune -a`.

Q. How do I clear HTTPS redirect caching in Chrome?

A. When Chrome detects an HTTP to HTTPS redirect, Chrome will no longer go to the backend without doing the redirect from within the browser. When you need to test the HTTPS redirect in our app that are working correctly, you would need to clear this cache so that the client side application can be instructed by the server side to redirect.

-   Go to `chrome://net-internals`.
-   Navigate to the Domain Security Policy, which will be located at the bottom of the left-hand side navbar.
-   Scroll to the bottom of the page. In the "Delete domain security policies" section, type in the domain of the cache you wish to delete.
-   Press Delete.

*Note: To check that you have deleted the domain cache properly, you can scroll to the top to the Query HSTS/PKP Domain section. Type in the domain again, and press Query. You have deleted the domain cache properly, if the result from the query is Not found.*


## Auth0

Q. What is Auth0 and why do I need it?

A. Auth0 is a third-party service that provides authentication for the different environments in Police Data Manager. You will use these credentials to log in to the higher environments.


## End-To-End Testing with Nightwatch

Q. How do I run end-to-end testing?

A. Run `yarn e2e`. The tests are headless, so you won't see the Chrome driver or any test UI appear.

Q. How do I investigate a failed E2E test if there is no visible Chrome browser window?

A. Snapshots of failed tests are taken as if the browser were open. These can be located in `E2E/tests/userJourney`. Each snapshot comes labeled with a timestamp and the name of the test that failed. After a failure, no subsequent E2E tests will run.

Q. Help! My E2E test suite has failed. What do I do?

A. Run it again. End-to-end tests can be flaky, especially if you have just run `docker-compose up app`. If the same test repeatedly fails, then look for a snapshot (see above), and investigate. You may also want to check that your local machine's variables are correctly set (see section on Local Environment). If your environment variables are correctly set, ensure your Auth0 account has DPM privileges, which are required to successfully run tests.


## Migrations

Q. In what order do migrations run?

A. Sequelize migrations run first and in the order in which they were created. These migrations create the database schema and are generated via the terminal (see below). Task migrations run afterwards in the order we give them and are created manually in the codebase. These migrate data into the schema.

Q. How do I generate a Sequelize (schema) migration from the terminal?

A. Run `yarn sequelize migration:generate --name name-of-your-migration`. This will automatically generate a file for you to create your up and down migration.

Q. How do I run up Sequelize migrations in my local environment from the terminal?

A. These are schema migrations. Run `NODE_ENV=development docker-compose run app yarn sequelize db:migrate` to run all migrations.

Q. How do I run down Sequelize migrations in my local environment from the terminal?

A. Run `NODE_ENV=development docker-compose run app yarn sequelize db:migrate:undo`. Unlike running the Sequelize up migration command, this will undo one migration at a time.

Q. How do I run down Sequelize migrations in higher environments?

A. Use the "Run console" option in the respective environment of the Heroku Dashboard. Start a bash shell and then use the `yarn sequelize db:migrate:undo`, this will revert the last run migration.

Q. How do I create a seed migration in Sequelize via the terminal?

A. Run `yarn sequelize seed:generate --name seed-name` to automatically create the file in the seeder folder.  When you run `docker-compose up app`, the migrations and the seeds will run, automatically seeding the database. 

Q. How do I see migration transactions in development?

A. In `sequelize_config.js`, under "development" set logging to true. This should start logging the transactions in the command line when you run the app.

Q. How to force re-run migrations if docker isn't recognizing a change?

A. Delete the migration you want from the `sequelize_meta` table first, using PGAdmin.


## Seeders

Q. How do I add data to a seeder .csv file without disrupting existing data?

A. **WARNING: DO NOT UNDO THE SEEDER!** This will lead to reference ID integrity being SEVERELY compromised.

-   Login to the AWS Console and navigate to the S3 folder containing the seeder files.
-   Download the existing seeder file you are updating and append your new data rows to the end.
-   You can then delete the now outdated seeder file from S3 and upload the new .csv file with the same name.

For each environment you wish to re-seed data for:

-   Connect to the database for said environment and run the following command: `DELETE FROM sequelize_data WHERE name='<seeder-file-name>';`
-   Check that this seeder file name is no longer in the `sequelize_data` table.

Re-run seeder:

-   Local: Run a `docker-compose up app` command.
-   Higher Environments: Access the console for said environment on Heroku and run the following command: `yarn run sequelize db:seed:all`

*Note: Check that new data appears in the corresponding database and any expected changes in app UI appear*


## Inserting CI Data into Your Local Environment

Q. I want to use CI data in my local environment. How do I do that?

A. You will need access to data.heroku and PDM's Heroku credentials to retrieve the data. Once you are logged in, select CI database. Under the "Durability" tab at left, click the  "Create Manual Backup", which appears on the bottom right side. Once the file has finished being created, click "Download". Once the file (a .dump) file has finished downloading to your Downloads folder, find its name (it should be a long string of characters). In terminal, type `pg_restore -h localhost -p 5432 -U postgres -1 ~/Downloads/backup_file_name_here.dump`, replacing `backup_file_name_here.dump` with the name of your backup file. Hit enter and watch the data transfer!


## Serving the Client Side from the Server in Your Local Environment

Q. I want to serve the front end (client side) statically from the back end server in order to approximate the other environments in the development pipeline. How do I do that?

A. In docker-compose.yml under `services > app > build > volumes`, uncomment out the command `- ./build:/app/build` to mount the build file. Under `service > app > build > environments`, change `REACT_APP_ENV=development` to `REACT_APP_ENV=static_development`. You then need to build your front end using the command `REACT_APP_ENV=static_development yarn build` in your terminal. This will build your front end statically &#x2013; meaning you will lose hot loading capabilities; any subsequent changes in front end code will necessitate a rebuilding of your frontend or removing these changes and running front and back ends on separate ports. After the build simply run `docker-compose up app` to run the app from port 1234.


## Accessing the Local Database Without Using pgAdmin

Q. I want to enter the running local db container without pgAmin but with psql instead. How do I do that?

A. `docker-compose exec db psql -U postgres -d noipm-db`

Q. How do I access the local database logs?

A. `docker-compose logs db`


## CircleCI Pipeline

*Note: Instructions for installing CircleCI CLI can be found here.*
Q. How do I run E2E tests against CI with CircleCI locally?

A. Un-comment the "Install Git" step in E2E-testing-ci-pdm job (`.circleci/config.yml`) and run the following command:
`circleci local execute -c .circleci/config.yml -e CI_TEST_PASS=<test-pass> -e CI_TEST_USER=<test-user> --job E2E-testing-ci-pdm`

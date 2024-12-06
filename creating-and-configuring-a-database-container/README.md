<h1>
  <span class="headline">Building a RESTful Microservice with Express</span>
  <span class="subhead">Creating and Configuring a Database Container</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to set up and configure a local database container for development and connect an Express application to the database.

## Adding a local database container

A microservice often needs a database to store and retrieve data. In this step, we'll create a local database container using Amazon DynamoDB's local image. This setup is ideal for development since the data stays on your computer, making testing easy and secure. Later, we’ll configure a cloud-based DynamoDB instance for production.

### 1. Update the `docker-compose.yml` file

First, we’ll update our `docker-compose.yml` file to include a new service for DynamoDB. This ensures that both our app and database containers are created and managed together.

Add the following to your `docker-compose.yml`:

```yml
version: '3.8'

services:
  accounts-api-app:
    container_name: accounts-api-app
    restart: always
    build:
      context: .
      dockerfile: Dockerfile
      args:
        - NODE_ENV=${NODE_ENV}
        - PORT=${PORT}
    ports:
      - '${PORT}:${PORT}'
    volumes:
      - ./:/usr/src/app
      - /usr/src/app/node_modules
    environment:
      - PORT=${PORT}
  dynamodb:
    container_name: dynamodb
    image: amazon/dynamodb-local
    ports:
      - '8000:8000'
    volumes:
      - dynamodb-data:/home/dynamodblocal
volumes:
  dynamodb-data:
```

### 2. Run the containers

Run the updated setup with the following command:

```sh
docker-compose up --build
```

To check the running containers, run:

```sh
docker ps
```

You should see two containers running: `dynamodb` (for the database) and `accounts-api-app` (for your application).

## Connecting the app to the database

Now that the database is running locally, we need to connect our Express app to it.

### 1. Create a database service

Let's create a folder in the root of our project called `services` and inside services, let's create a file called `database.js`:

```sh
mkdir services
touch services/database.js
```

Paste the following code into `database.js`:

```js
import { DynamoDBClient } from '@aws-sdk/client-dynamodb';
import { DynamoDBDocumentClient } from '@aws-sdk/lib-dynamodb';

const client = new DynamoDBClient({
  region: 'us-east-1',
  endpoint: 'http://dynamodb:8000', // Connect to the local database container
  credentials: {
    accessKeyId: process.env.AWS_ACCESS_KEY_ID || 'dummy',
    secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY || 'dummy',
  },
});

const database = DynamoDBDocumentClient.from(client);

export default database;
```

> Note: The `endpoint` points to the `dynamodb` container using its service name, `http://dynamodb:8000`. Docker Compose automatically resolves this name, making local development easier and mimicking a production-like connection setup.

### 2. Update the Controller

Let's update our controller to connect to the database. We’ll begin by altering one controller function to test the connection. Open `accountsController.js` and modify the file as shown below. For now the changes will only be in the imports and the `getAllAccounts` function.

In `accountsController.js` replace all existing code with the following:

```js
import database from '../services/database.js'; // Import the database connection from the newly created database service
import { ScanCommand } from '@aws-sdk/lib-dynamodb'; // Import ScanCommand for querying the DynamoDB table

async function getAllAccounts(req, res, next) {
  try {
    const params = {
      TableName: 'Accounts', // Specify the name of the table to scan
    };
    const command = new ScanCommand(params); // Create a new ScanCommand to get all items from the table
    const result = await database.send(command); // Send the command to DynamoDB and await the result
    res.status(200).json(result.Items); // Respond with the items found in the table
  } catch (err) {
    next(err); // Pass errors to the error-handling middleware
  }
}

function createAccount(req, res) {
  res.send(
    '🤖 Accounts Route with POST method - this endpoint will create a new account in the database'
  );
}

function getAccountById(req, res) {
  const accountId = req.params.id;
  res.send(
    '🤖 Accounts Route with GET method - this endpoint will get a single account by ID from the database. The account is: ' +
      accountId
  );
}

function updateAccountById(req, res) {
  const accountId = req.params.id;
  res.send(
    '🤖 Accounts Route with PUT method - this endpoint will update a single account by ID from the database. The account is: ' +
      accountId
  );
}

function deleteAccountById(req, res) {
  const accountId = req.params.id;
  res.send(
    '🤖 Accounts Route with DELETE method - this endpoint will delete a single account by ID from the database. The account is: ' +
      accountId
  );
}

export default {
  getAllAccounts,
  createAccount,
  getAccountById,
  updateAccountById,
  deleteAccountById,
};
```

## Testing the Connection

Testing confirms that our app and database are correctly connected and configured.

Send a `GET` request to `http://localhost:3000/accounts`. You’ll see an error:

```sh
ResourceNotFoundException: Cannot do operations on a non-existent table
```

> This error is expected. It indicates that we can reach the route, but the `Accounts` table does not exist yet. This is true; we need to create the table in our database before we can perform read or write operations.

### Next Steps

In the next section, we’ll use tools like Ansible to create and configure the necessary tables in DynamoDB.

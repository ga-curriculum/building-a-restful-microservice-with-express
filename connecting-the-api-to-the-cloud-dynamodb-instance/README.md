<h1>
  <span class="headline">Building a RESTful Microservice with Express</span>
  <span class="subhead">Connecting the API to the Cloud DynamoDB Instance</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to configure and connect a containerized Node.js API to a cloud-based DynamoDB instance on AWS.

## Connecting our containerized accounts API to AWS DynamoDB

Now that we have a DynamoDB table provisioned in AWS, let's connect our containerized Accounts API to it. This involves updating our codebase to work with the AWS DynamoDB instance and configuring our environment variables to securely access the database.

### 1. Update the `database.js` file

Replace the contents of your `database.js` file with the following code:

```js
// Added import for ListTablesCommand to enable listing DynamoDB tables
import { DynamoDBClient, ListTablesCommand } from '@aws-sdk/client-dynamodb';
import { DynamoDBDocumentClient } from '@aws-sdk/lib-dynamodb';

const client = new DynamoDBClient({
  region: 'us-east-1',
  // Modified: Use an environment variable for the endpoint, with a fallback to local DynamoDB
  endpoint: process.env.DYNAMODB_ENDPOINT || 'http://dynamodb:8000',
  credentials: {
    accessKeyId: process.env.AWS_ACCESS_KEY_ID || 'dummy',
    secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY || 'dummy',
  },
});

const database = DynamoDBDocumentClient.from(client);

// New function: Allows listing of all tables in the connected DynamoDB instance
export async function listDatabaseTables() {
  try {
    const command = new ListTablesCommand({}); // Creates a command to list tables
    const response = await database.send(command); // Executes the command and fetches the table names
    console.log('Tables:', response.TableNames); // Logs the table names
  } catch (error) {
    console.error('Error listing tables:', error); // Logs any errors encountered
  }
}

export default database;
```

What we changed:

- **New Import:** Added `ListTablesCommand` to enable listing tables.
- **Environment Variable:** Updated `endpoint` to use `process.env.DYNAMODB_ENDPOINT` for flexibility between local and cloud DynamoDB.
- **New Function:** Added `listDatabaseTables` to demonstrate how to list tables in the DynamoDB instance.

### 2. Update the `.env` file

Now modify your `.env` file to include the following environment variables:

```dotenv
PORT=3000
NODE_ENV=dev
AWS_ACCESS_KEY_ID="your aws access key from whizlabs"
AWS_SECRET_ACCESS_KEY="your aws secret access key from whizlabs"
DYNAMODB_ENDPOINT=https://dynamodb.us-east-1.amazonaws.com
```

What we added:

- **AWS_ACCESS_KEY_ID** and **AWS_SECRET_ACCESS_KEY**: These provide authentication to your AWS account. Obtain these from Whizlabs as instructed in the previous section.
- **DYNAMODB_ENDPOINT**: Points to the AWS-hosted DynamoDB service.

### 3: Run the Docker container

Use the following command to run your containerized API, passing in the environment variables:

```sh
docker run -p 3000:3000 \
 -e NODE_ENV=dev \
 -e PORT=3000 \
 -e AWS_ACCESS_KEY_ID=[your access key id] \
 -e AWS_SECRET_ACCESS_KEY=[your secret access key] \
 your-container-name
```

Things to note:

- **Environment Variables with `-e`**: Passes the required variables securely into the container at runtime.
- **Port Mapping (`-p`)**: Maps port 3000 on the container to port 3000 on your host machine for accessibility.

### Next steps

Once the container is running, your API will be connected to the AWS DynamoDB instance and ready for final testing. Test the connection by interacting with the API endpoints and ensure the data is correctly stored and retrieved from the cloud database.

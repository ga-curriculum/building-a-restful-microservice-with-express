<h1>
  <span class="headline">Building a RESTful Microservice with Express</span>
  <span class="subhead">Refactoring Controllers for CRUD with DynamoDB</span>
</h1>

**Learning Objective:** By the end of this lesson, students will be able to refactor controller functions for a RESTful API to perform CRUD operations with DynamoDB.

## Enforcing data consistency with schemas

In DynamoDB, the database does not enforce a schema, meaning it won’t automatically check the structure or data types of the data we store. To make sure we get consistent and valid data, we will use a package called `joi` for data validation. [Joi](https://www.npmjs.com/package/joi) allows us to define a schema, which is a blueprint that specifies what data should look like (ex: required fields, data types). By enforcing a schema, we know that the data we pass into our controller functions meets our expectations before saving it to the database.

### 1. Create a new directory and file for `Account` schema

We installed `joi` when we first set up our application, so let’s use it now to define a schema. This schema will help us validate the data, making sure only the correct fields are included and formatted properly.

Then, we will refactor our controller functions to use `joi` for data validation. This will help us create, read, update, and delete accounts in DynamoDB while maintaining data consistency and enforcing our pre defined standards.

Create a new directory called `models` and a file named `account.js` inside it. This file will define the data schema for an account.

```bash
mkdir models;
touch models/account.js;
```

### 2. Define the `Account` schema

Copy the following code into `models/account.js` to create a `joi` schema for account validation.

```js
import joi from 'joi';

const accountSchema = joi.object()({
  id: joi.string().required(),
  sort_code: joi.string().required(),
  account_number: joi.number().required(),
  balance: joi.number().required(),
});

export default accountSchema;
```

This schema ensures that any `account` data must have:

- `id`: a string that is required.
- `sort_code`: a string that is required.
- `account_number`: a number that is required.
- `balance`: a number that is required.

## Update the controller functions

Now we need to modify all of the controller functions to use `joi` for validating request data before sending it to DynamoDB.

We'll also use the `uuid` library to generate unique IDs for each new account before sending the data to DynamoDB.

Replace all the code in your controller file with the following:

```js
import database from '../services/database.js';
import {
  ScanCommand,
  PutCommand,
  GetCommand,
  UpdateCommand,
  DeleteCommand,
} from '@aws-sdk/lib-dynamodb';
// Import the 'uuid' library to generate unique IDs for each account
import { v4 as uuidv4 } from 'uuid';
// Import the account schema for validating the account data
import accountSchema from '../models/account.js';

async function getAllAccounts(req, res, next) {
  try {
    const params = {
      TableName: 'Accounts',
    };
    const command = new ScanCommand(params);
    const result = await database.send(command);
    res.status(200).json(result.Items);
  } catch (err) {
    next(err);
  }
}

async function createAccount(req, res, next) {
  try {
    // Generate a unique ID for the new account
    const uuid = uuidv4();
    req.body.id = uuid;

    // Validate the request body against the schema
    const { error, value } = accountSchema.validate(req.body);

    if (error) {
      // Respond with a 400 status and validation error if the data is invalid
      res.status(400).json({ error: error.details[0].message });
      return;
    }

    const { id, sort_code, account_number, balance } = value;

    // Create the new account in DynamoDB
    const params = {
      TableName: 'Accounts',
      Item: {
        id,
        sort_code,
        account_number,
        balance,
      },
    };

    const command = new PutCommand(params);
    await database.send(command);

    res
      .status(201)
      .json({ message: 'Successfully created account', data: params.Item });
  } catch (error) {
    next(error);
  }
}

async function getAccountById(req, res, next) {
  const accountId = req.params.id;
  try {
    const params = {
      TableName: 'Accounts',
      Key: { id: accountId },
    };
    const command = new GetCommand(params);
    const result = await database.send(command);
    if (!result.Item) {
      return res.status(404).json({ message: 'No account found' });
    }
    res.status(200).json(result.Item);
  } catch (err) {
    next(err);
  }
}

async function updateAccountById(req, res, next) {
  try {
    const accountId = req.params.id;
    req.body.id = accountId;

    // Validate the request body against the schema
    const { error, value } = accountSchema.validate(req.body);

    if (error) {
      return res.status(400).json({ error: error.details[0].message });
    }

    const { balance, sort_code, account_number } = value;

    // Retrieve the existing account from DynamoDB
    const getParams = {
      TableName: 'Accounts',
      Key: { id: accountId },
    };
    const getCommand = new GetCommand(getParams);
    const result = await database.send(getCommand);

    const account = result.Item;
    if (!account) {
      return res.status(404).json({ message: 'No account found' });
    }

    // Update the account in DynamoDB
    const updateParams = {
      TableName: 'Accounts',
      Key: { id: accountId },
      UpdateExpression:
        'set #balance = :balance, #sort_code = :sort_code, #account_number = :account_number',
      ExpressionAttributeNames: {
        '#balance': 'balance',
        '#sort_code': 'sort_code',
        '#account_number': 'account_number',
      },
      ExpressionAttributeValues: {
        ':balance': balance,
        ':sort_code': sort_code,
        ':account_number': account_number,
      },
      ReturnValues: 'ALL_NEW',
    };
    const updateCommand = new UpdateCommand(updateParams);
    const updatedAccount = await database.send(updateCommand);

    res.status(200).json(updatedAccount.Attributes);
  } catch (err) {
    next(err);
  }
}

async function deleteAccountById(req, res, next) {
  const accountId = req.params.id;
  try {
    const params = {
      TableName: 'Accounts',
      Key: { id: accountId },
    };
    const command = new DeleteCommand(params);
    await database.send(command);
    res.status(204).end();
  } catch (err) {
    next(err);
  }
}

export default {
  getAllAccounts,
  createAccount,
  getAccountById,
  updateAccountById,
  deleteAccountById,
};
```

> Note: This code example is provided as a complete, working implementation to help you understand how to structure a microservice. We recognize that this is a lot of code, and it's perfectly okay if you don't fully understand every line. The goal of this lesson is not to make you an expert in building Express applications but to show you how microservices are structured and how they interact with a database like DynamoDB.
>
> We have provided this comprehensive example because breaking it down line-by-line would be too extensive for this lesson's scope. Our aim is to give you the code needed to build out a working microservice for demonstration and learning purposes. Don't worry if some parts are unclear; the key takeaway is learning the overall structure and flow of a microservice, not mastering every detail of the Express framework.

## Test the functions

Use Postman to test all the CRUD operations (GET, POST, PUT, DELETE). Ensure that each endpoint is working as expected and data validation is correctly enforcing the schema.

**_We've provided a few examples below to get you started!_**

### Example: Create `Account` request

- Endpoint: `POST` `/accounts`

Sample request body:

```json
{
  "sort_code": "123456",
  "account_number": 987654321,
  "balance": 500.75
}
```

Expected Response:

```json
{
  "message": "Successfully created account",
  "data": {
    "id": "e7a4eae4-6d46-4f33-8b6e-7a54b1d9c8f2", // This is a generated unique UUID that identifies the account
    "sort_code": "123456",
    "account_number": 987654321,
    "balance": 500.75
  }
}
```

> Note: The `id` field above is a unique identifier for the newly created account. Save this UUID for future use, such as when making requests to read, update, or delete the account by its ID.

### Example: Get `Account` by ID request

- Endpoint: `GET` `/accounts/:id`
- Sample Request: Replace `:id` with a valid account ID `/accounts/e7a4eae4-6d46-4f33-8b6e-7a54b1d9c8f2`

Expected Response:

```json
{
  "id": "e7a4eae4-6d46-4f33-8b6e-7a54b1d9c8f2",
  "sort_code": "123456",
  "account_number": 987654321,
  "balance": 500.75
}
```

### Example: Update `Account` by ID request

- Endpoint: `PUT` `/accounts/:id`
- Sample Request: Replace `:id` with a valid account ID `/accounts/e7a4eae4-6d46-4f33-8b6e-7a54b1d9c8f2`

_(Lets update the account balance from 500.75 to 625.50)_

Sample Request Body:

```json
{
  "sort_code": "123456",
  "account_number": 987654321,
  "balance": 625.5
}
```

Expected Response:

```json
{
  "id": "e7a4eae4-6d46-4f33-8b6e-7a54b1d9c8f2",
  "sort_code": "123456",
  "account_number": 987654321,
  "balance": 625.5
}
```

<h1>
  <span class="headline">Building a Microservice with Express JS</span>
  <span class="subhead">Developing CRUD Endpoints with Express Controllers</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to build and organize controller functions to handle CRUD operations in an Express app.

## 1. Create a new controller file

Controller functions are responsible for processing incoming requests and returning responses. We will create a new file, `accountsController.js`, to hold these functions.

1. Create a folder named `controllers` and add a file called `accountsController.js`:

```bash
mkdir controllers
touch controllers/accountsController.js
```

## 2. Add controller functions

Add the following code to `controllers/accountsController.js`. These functions will handle the logic for getting all accounts, creating a new account, and processing individual account requests.

```js
function getAllAccounts(req, res) {
  res.send(
    '🤖 Accounts Route with GET method - this endpoint will get all of the accounts from the database'
  );
}

function createAccount(req, res) {
  res.send(
    '🤖 Accounts Route with POST method - this endpoint will create a new account in the database'
  );
}

export default {
  getAllAccounts,
  createAccount,
};
```

## 3. Update the router to use the controller

To use these controller functions, we need to import them into `router.js` and connect them to the appropriate routes.

1. Update `router.js` to use the functions from `accountsController.js`:

```js
import express from 'express';
import accountsController from '../controllers/accountsController.js';

const Router = express.Router();

Router.route('/accounts')
  .get(accountsController.getAllAccounts) // notice how this refactor simplifies our code
  .post(accountsController.createAccount);

export default Router;
```

## 4. Add Routes for Individual Account Operations

Next, we'll create controller functions that handle operations for a specific account, such as fetching, updating, and deleting by ID. This will give us a complete set of CRUD functions for our accounts service.

1. Update `controllers/accountsController.js` to include these new functions:

```js
function getAllAccounts(req, res) {
  res.send(
    '🤖 Accounts Route with GET method - this endpoint will get all of the accounts from the database'
  );
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

## 5. Update the router with new endpoints

Now, modify `router.js` to include the new routes for handling specific accounts by ID.

1. Update `router.js`:

```js
import express from 'express';
import accountsController from '../controllers/accountsController.js';

const Router = express.Router();

Router.route('/accounts')
  .get(accountsController.getAllAccounts)
  .post(accountsController.createAccount);

Router.route('/accounts/:id')
  .get(accountsController.getAccountById) // new
  .put(accountsController.updateAccountById) // new
  .delete(accountsController.deleteAccountById); // new

export default Router;
```

## 6. Review and push to version control (Optional)

Your Express app now includes endpoints for basic CRUD operations. To keep the project clean, create a `.gitignore` file to exclude `node_modules` and push your code to a repository:

1. Create a `.gitignore` file with the following content:

```
node_modules/
```

2. Commit and push your code to your version control system.

## Summary

In this lesson, you built an Express.js application with mock CRUD routes to simulate a microservice for managing accounts. You created an organized project structure with controllers handling the logic for each route. This foundational setup allows you to extend and develop more complex functionalities, such as connecting to a database or adding authentication.

<h1>
  <span class="headline">Building a RESTful Microservice with Express</span>
  <span class="subhead">Testing and Reviewing Your API</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to evaluate the functionality, reliability, and integration of a RESTful API with cloud services and apply best practices for testing and reviewing software.

## Testing and Review

Congratulations on reaching this milestone!

By the end of this process, you’ve achieved the following:

- Developed a **fully functional, cloud-connected RESTful API** for managing accounts.
- Built a **DynamoDB-backed persistence layer** for storing and retrieving data.
- Created reusable **Infrastructure-as-Code templates** to streamline future cloud deployments.

### Review the API

Use the following routes table to guide your review and testing of the API.

| **HTTP Method** | **Endpoint**    | **Description**                                | **Request Body**                                                  | **Response**                                                                         |
| --------------- | --------------- | ---------------------------------------------- | ----------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| `GET`           | `/accounts`     | Retrieve a list of all accounts.               | None                                                              | `200 OK`: List of accounts.                                                          |
| `GET`           | `/accounts/:id` | Retrieve details for a specific account by ID. | None                                                              | `200 OK`: Account details.<br>`404 Not Found`: If account is not found.              |
| `POST`          | `/accounts`     | Create a new account.                          | `{ "name": "string", "email": "string", "balance": "number" }`    | `201 Created`: Newly created account.<br>`400 Bad Request`: If validation fails.     |
| `PUT`           | `/accounts/:id` | Update an existing account by ID.              | `{ "name": "string", "email": "string", "balance": "number" }`    | `200 OK`: Updated account details.<br>`404 Not Found`: If account is not found.      |
| `PATCH`         | `/accounts/:id` | Partially update an existing account by ID.    | `{ "balance": "number" }` (or any field that needs to be updated) | `200 OK`: Updated account details.<br>`404 Not Found`: If account is not found.      |
| `DELETE`        | `/accounts/:id` | Delete a specific account by ID.               | None                                                              | `204 No Content`: Successfully deleted.<br>`404 Not Found`: If account is not found. |

Now it’s time to test your API and observe how the data interacts with your AWS DynamoDB production database!

## Wrap up

Congratulations on successfully building a fully functional Microservice!

Let's highlight some key achievements from this project:

### 1. **Setting Up a Containerized API**

- You developed a foundational Express-based RESTful API to manage account-related data.
- You containerized the application using Docker, ensuring it runs consistently across different environments.
- You securely managed environment variables to configure application settings.

### 2. **Implementing CRUD Operations**

- You created routes to handle creating, reading, updating, and deleting account resources.
- You worked with HTTP methods (`GET`, `POST`, `PUT`, `PATCH`, `DELETE`) and status codes to build a robust API.
- You structured the API to follow RESTful principles, enhancing scalability and clarity.

### 3. **Provisioning Cloud Infrastructure with Terraform**

- You used Terraform to define and deploy an AWS DynamoDB table.
- You standardized cloud resource management by leveraging Infrastructure as Code (IaC).
- You configured AWS CLI credentials for secure interactions with AWS services.

### 4. **Connecting the API to DynamoDB**

- You integrated the API with a DynamoDB instance in AWS, enabling persistent data storage.
- You used the AWS SDK to interact programmatically with DynamoDB.
- You securely handled AWS credentials through environment variables.

## Final steps... cleaning up resources

As a best practice, it’s important to destroy any cloud infrastructure created for experimentation or development. This prevents unnecessary costs and ensures a clean environment for future work. In the next section, you’ll learn how to safely destroy the resources you provisioned in AWS.

**_Dont skip this step!_**

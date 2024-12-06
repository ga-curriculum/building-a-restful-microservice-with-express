<h1>
  <span class="headline">Building a RESTful Microservice with Express</span>
  <span class="subhead">Deploying a DynamoDB Table with Ansible</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to create and configure a DynamoDB table using Ansible to support CRUD operations for their microservice.

## Create the Ansible `playbook` file

To proceed, we'll create an Ansible `playbook` that sets up a DynamoDB table. This will enable our application to read from and write to the database.

Create a file named `create_accounts_table.yml` in the root of your project and paste the following code into it:

```yml
---
- name: Create DynamoDB Table in Docker Container
  hosts: localhost
  gather_facts: no
  vars:
    dynamodb_endpoint: 'http://localhost:8000' # URL where DynamoDB is running
    aws_access_key_id: 'dummy' # Placeholder AWS credentials for local development
    aws_secret_access_key: 'dummy'
    table_name: 'Accounts' # Name of the table to be created
    region: 'us-east-1' # AWS region

  tasks:
    - name: Create the Accounts DynamoDB table using AWS CLI
      command: >
        aws dynamodb create-table
        --table-name {{ table_name }}
        --attribute-definitions AttributeName=id,AttributeType=S
        --key-schema AttributeName=id,KeyType=HASH
        --billing-mode PROVISIONED
        --provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=5
        --endpoint-url {{ dynamodb_endpoint }}
      environment:
        AWS_ACCESS_KEY_ID: '{{ aws_access_key_id }}'
        AWS_SECRET_ACCESS_KEY: '{{ aws_secret_access_key }}'
      register: dynamodb_result # Store the result of table creation

    - name: Show detailed result of table creation
      debug:
        var: dynamodb_result # Print out the details of the table creation

    - name: List tables in DynamoDB
      command: >
        aws dynamodb list-tables
        --endpoint-url {{ dynamodb_endpoint }}
      environment:
        AWS_ACCESS_KEY_ID: '{{ aws_access_key_id }}'
        AWS_SECRET_ACCESS_KEY: '{{ aws_secret_access_key }}'
      register: list_tables_result # Store the result of listing tables

    - name: Show list of tables
      debug:
        var: list_tables_result # Print out the list of existing tables
```

## Run the Ansible `playbook`

In your terminal, run the following command to execute the `playbook`:

```bash
ansible-playbook create_accounts_table.yml
```

<blockquote class="attention">
⚠️  Important: Make sure your containers are running before you execute this command. This ensures that the DynamoDB instance is active and available to process the table creation request.
</blockquote>

Running the `playbook` will create the `Accounts` table in your local DynamoDB instance. You should see a confirmation message and details from the debug tasks indicating the successful creation of the table.

### Test the API

Once the table is set up, you can test your API by making requests to it. If you send a `GET` request to the `/accounts` endpoint again, you'll receive an empty array response (since no data has been added to the table yet).

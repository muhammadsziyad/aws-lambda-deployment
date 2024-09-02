# aws-lambda-deployment

Creating a project application using AWS Lambda involves setting up a serverless architecture where Lambda functions handle specific tasks, and other AWS services integrate to support the application. Here's a step-by-step guide to build a simple serverless web application with AWS Lambda, API Gateway, S3, and DynamoDB.

### Project Overview

We'll create a simple "Todo List" application where users can:

- Add new tasks
- Retrieve existing tasks
- Delete tasks

We'll use the following AWS services:

- **AWS Lambda**: To handle the application logic
- **Amazon API Gateway**: To create RESTful APIs
- **Amazon DynamoDB**: To store task data
- **Amazon S3**: To host a static web front-end (optional)

### Step 1: Set Up DynamoDB Table

1. **Navigate to DynamoDB**:
    
    - In the AWS Management Console, search for “DynamoDB” and select it.
2. **Create a Table**:
    
    - Click on “Create table.”
    - Enter a table name, e.g., `TodoTable`.
    - Set the primary key. For example, use `taskId` (String) as the partition key.
    - Configure any additional settings as needed.
    - Click “Create table.”

### Step 2: Create Lambda Functions

1. **Navigate to Lambda**:
    
    - In the AWS Management Console, search for “Lambda” and select it.
2. **Create a Lambda Function for Adding Tasks**:
    
    - Click “Create function.”
    - Choose “Author from scratch.”
    - Enter a function name, e.g., `AddTaskFunction`.
    - Choose a runtime, e.g., Python 3.x.
    - Create a new role with basic Lambda permissions or use an existing one.
3. **Add Code to Handle Task Addition**:
    
    - Example Python code:

```python
import json
import boto3
from uuid import uuid4

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('TodoTable')

def lambda_handler(event, context):
    body = json.loads(event['body'])
    task_id = str(uuid4())
    task_description = body.get('description')

    table.put_item(
        Item={
            'taskId': task_id,
            'description': task_description
        }
    )

    return {
        'statusCode': 200,
        'body': json.dumps({'taskId': task_id})
    }

```
        
4. **Create Lambda Function for Retrieving Tasks**:
    
    - Repeat the process to create another Lambda function, e.g., `GetTasksFunction`.
5. **Add Code to Retrieve Tasks**:
    
    - Example Python code:

```python
import json
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('TodoTable')

def lambda_handler(event, context):
    response = table.scan()
    items = response.get('Items', [])

    return {
        'statusCode': 200,
        'body': json.dumps(items)
    }

```
        
   
6. **Create Lambda Function for Deleting Tasks**:
    
    - Repeat the process to create another Lambda function, e.g., `DeleteTaskFunction`.
7. **Add Code to Delete Tasks**:
    
    - Example Python code:
        
```python
import json
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('TodoTable')

def lambda_handler(event, context):
    task_id = event['pathParameters']['taskId']

    table.delete_item(
        Key={
            'taskId': task_id
        }
    )

    return {
        'statusCode': 200,
        'body': json.dumps({'message': 'Task deleted successfully'})
    }

```
        

### Step 3: Set Up API Gateway

1. **Navigate to API Gateway**:
    
    - In the AWS Management Console, search for “API Gateway” and select it.
2. **Create a New API**:
    
    - Click “Create API” and choose “HTTP API” or “REST API.”
    - Configure the API with a name, e.g., `TodoAPI`.
3. **Create Resources and Methods**:
    
    - For HTTP API:
        - Define routes, e.g., `/tasks` (POST for adding, GET for retrieving).
        - Configure integrations to connect the routes to Lambda functions.
    - For REST API:
        - Create resources (e.g., `/tasks`).
        - Create methods (e.g., POST, GET, DELETE) and link them to the corresponding Lambda functions.
4. **Deploy API**:
    
    - Create a new stage (e.g., `prod`) and deploy the API.

### Step 4: (Optional) Set Up a Static Web Front-End

1. **Create a Static Website**:
    
    - Develop a simple HTML/JavaScript front-end that interacts with the API Gateway endpoints.
2. **Host on S3**:
    
    - Navigate to S3, create a bucket for static website hosting.
    - Upload your HTML, CSS, and JS files.
    - Configure the bucket for static website hosting.
3. **Configure Bucket Policy**:
    
    - Set a bucket policy to allow public read access to the files.

### Step 5: Testing and Deployment

1. **Test API Endpoints**:
    
    - Use tools like Postman or Curl to test your API endpoints.
2. **Test the Web Front-End**:
    
    - Open your S3 website URL and ensure that the front-end interacts correctly with your Lambda functions.
3. **Monitor and Optimize**:
    
    - Use CloudWatch to monitor Lambda logs and performance.
    - Optimize Lambda function code and DynamoDB settings as needed.

### Summary

You now have a basic serverless "Todo List" application using AWS Lambda, API Gateway, DynamoDB, and optionally S3 for a static web front-end. This architecture allows you to scale seamlessly and manage resources efficiently.

Feel free to adjust the code and configurations based on your specific use case and requirements. Let me know if you need further details or have specific questions!

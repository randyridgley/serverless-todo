### Creating the Serverless application

#### Install serverless and npm
Serverless requires nodejs to be installed to leverage npm. Follow instructions on NodeJS website on how to install on your machine.
``` 
brew install node
npm install -g serverless
```

#### Create the folder and serverless project
We are going to start by creating a project folder and create the boilerplate code for our project based on the aws-python3 template provided by serverless.

The project creates the serverless.yml file and the default handler. We will use Visual Studio Code to make changes to the application.

``` 
mkdir {project-folder} 
cd {project-folder}
sls create --template aws-python3 --name {project-folder}
code .
```

we will add the API gateway endpoint to be able to test the API and the lambda function. Edit the serverless.yml file and on the function hello add the event like below:

```
  events:
    - http:
        path: /hello
        method: get
        cors: true
```
After editing the file we will deploy the project.

```
sls deploy -v
```
After we create and deploy the project we will install httpie to test the endpoint to exercise the default method.

```
pip install httpie
http https://{api-url}/dev/hello
```

### We will now make changes to the hello world project and create a todo application that leverages Cognito for user authentication.

We will start with creating the CRUD methods for the todo app to create, read, update, delete, and list the todos. We will also be using a DynamoDB table to store the todos. 

In your project copy the todos folder over to your project and update the serverless.yml file with the new API endpoints.

```
create:
    handler: todos/create.create
    events:
      - http:
          path: todos
          method: post
          cors: true

  list:
    handler: todos/list.list
    events:
      - http:
          path: todos
          method: get
          cors: true

  get:
    handler: todos/get.get
    events:
      - http:
          path: todos/{id}
          method: get
          cors: true

  update:
    handler: todos/update.update
    events:
      - http:
          path: todos/{id}
          method: put
          cors: true

  delete:
    handler: todos/delete.delete
    events:
      - http:
          path: todos/{id}
          method: delete
          cors: true
```

Once you have copied the todo Lambda functions into your project and updated the serverless.yml file we are ready to redeploy the project with the updated methods and test them out.

```
sls deploy -v
# Create a todo
echo '{ "text": "My First Todo" }' | http POST https://{api-url}/dev/todos
http https://{api-url}/dev/todos

# List the todos available
http POST https://{api-url}/dev/todos
```

Now you can see how easy it is to create a microservice using Lambda but what if we wanted to only allow authenticated users to have the ability to create todos? We can create a Cognito user pool and use it as an authorizer to the API. The deployment should have already created the Cognito user pool, so we will use that and hook it up to the create method of the service. 

Change the serverless.yml file to add the authorizer like below.

```
    authorizer:
        type: COGNITO_USER_POOLS
        authorizerId:
            Ref: TodoApiGatewayAuthorizer
```

Let's redeploy the service and try and add a todo
```
sls deploy -v

echo '{ "text": "My First Todo" }' | http POST https://{api-url}/dev/todos
```
This should have returned an HTTP/1.1 401 Unauthorized because you did not send the appropriate Authorization header on the call. We will simulate a user logging into the application and passing the bearer token in the headers of the request. Running the 3 methods below will simulate creating a user, confirming the user, and generating the appropriate auth for the user to be able to create the todo. Grab the IdToken generated and add it to the headers like the command below.

```
# Create user in Cognito
aws cognito-idp sign-up --region {your-aws-region} --client-id {cognito-client-id} --username admin@example.com --password {password}

# Confirm the sign up of the user
aws cognito-idp admin-confirm-sign-up --region {your-aws-region} --user-pool-id {cognito-user-pool} --username admin@example.com

#Initiate the authentication to generate the token
aws cognito-idp admin-initiate-auth --region {your-aws-region} --cli-input-json file://auth.json

echo '{ "text": "My Authenticated Todo" }' | http POST https://{api-url}/dev/todos Authorization:"Bearer {your-idtoken}"
``` 
Now you should have created a new todo after the authorizer approves the token.

### Register a user

> aws cognito-idp sign-up --region {your-aws-region} --client-id {your-client-id} --username admin@example.com --password {password}

### Confirm user registration

> aws cognito-idp admin-confirm-sign-up --region {your-aws-region} --user-pool-id {your-user-pool-id} --username admin@example.com

### Authenticate (get tokens)

> aws cognito-idp admin-initiate-auth --region {your-aws-region} --cli-input-json file://auth.json

### Where auth.json is:

>{
    "UserPoolId": "{your-user-pool-id}",
    "ClientId": "{your-client-id}",
    "AuthFlow": "ADMIN_NO_SRP_AUTH",
    "AuthParameters": {
        "USERNAME": "admin@example.com",
        "PASSWORD": "{password}"
    }
}

### You should get a response like this if everything is set up correctly:

>{
    "AuthenticationResult": {
        "ExpiresIn": 3600,
        "IdToken": "{your-idtoken}",
        "RefreshToken": "{your-refresh-token}",
        "TokenType": "Bearer",
        "AccessToken": "{your-access-token}"
    },
    "ChallengeParameters": {}
}
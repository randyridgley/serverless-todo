### Creating the Serverless application

``` 
mkdir {project-folder} 
cd {project-folder}
sls create --template aws-python3 --name {project-folder}
pip install httpie
code .
sls deploy -v
http https://{api-url}/dev/hello
echo '{ "text": "My First Todo" }' | http POST https://{api-url}/dev/todos

http https://{api-url}/dev/todos

aws cognito-idp sign-up --region {your-aws-region} --client-id {cognito-client-id} --username admin@example.com --password {password}

aws cognito-idp admin-confirm-sign-up --region {your-aws-region} --user-pool-id {cognito-user-pool} --username admin@example.com

 aws cognito-idp admin-initiate-auth --region {your-aws-region} --cli-input-json file://auth.json

 echo '{ "text": "Learn Serverless" }' | http POST https://{api-url}/dev/todos Authorization:"Bearer {your-idtoken}"
``` 

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
### Creating the Serverless application

``` 
mkdir hello-world 
cd hello-world
sls create --template aws-python3 --name hello-world
code .
sls deploy -v
http https://3rz2wxy8te.execute-api.us-east-1.amazonaws.com/dev/hello
echo '{ "text": "My First Todo" }' | http POST https://eumgrqs7ob.execute-api.us-east-1.amazonaws.com/dev/todos

http https://eumgrqs7ob.execute-api.us-east-1.amazonaws.com/dev/todos

aws cognito-idp sign-up --region us-east-1 --client-id quei59dcn7qpf9r42jeba89db --username admin@example.com --password Passw0rd!

aws cognito-idp admin-confirm-sign-up --region us-east-1 --user-pool-id us-east-1_28fgD17Qk --username admin@example.com

 aws cognito-idp admin-initiate-auth --region us-east-1 --cli-input-json file://auth.json

 echo '{ "text": "Learn Serverless" }' | http POST https://eumgrqs7ob.execute-api.us-east-1.amazonaws.com/dev/todos Authorization:"Bearer eyJraWQiOiJYXC9RbVNJV1U2NlJZVzZJTDdvZFFTVzZlelp6OU9TN3c3eTNuYUE1aVVXMD0iLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiI0YzQxMmRmOS0zNTIyLTQ4NjAtYmVmOC05YzViNTkxY2EzZmIiLCJhdWQiOiJxdWVpNTlkY243cXBmOXI0MmplYmE4OWRiIiwiZXZlbnRfaWQiOiI4ZGMzZjI4ZS1kZmFmLTExZTgtYmUyOS05OTRmNGNhZGFiMjUiLCJ0b2tlbl91c2UiOiJpZCIsImF1dGhfdGltZSI6MTU0MTI4MDU3MiwiaXNzIjoiaHR0cHM6XC9cL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tXC91cy1lYXN0LTFfMjhmZ0QxN1FrIiwiY29nbml0bzp1c2VybmFtZSI6ImFkbWluQGV4YW1wbGUuY29tIiwiZXhwIjoxNTQxMjg0MTcyLCJpYXQiOjE1NDEyODA1NzJ9.FY5eCb-MpV5ngFIKNPtpuEDBOeUV5DZd3roj6EiCfhWuKvN9DwwG4SqpLtUMbsBn_la2hHHDEx0SuVX9DRVtTHNNCViks7kr89r3wBqGrgIw9vlulddkgrSkylWUNcIvwPoEhLsaKhU8UlRGMk21SgHxt54J3NCS4GOMZBpBqQzzc1EJyVCcm41tRqnAu5nY8qXQSLvovI7-jT9wuttkkiWfWErmsz0AuXv_z9DWgCnCLvXTPMZ6gKhJ8guK_FjvwjjwOUsBrp791UA2HIM8ecYvJ60UOSCOG2SrX8uqM_LPE5GknjgjgFxZAbM267RHgRNv079h31Uzwthu_Gh79g"
``` 

### Register a user

> aws cognito-idp sign-up --region {your-aws-region} --client-id {your-client-id} --username admin@example.com --password Passw0rd!

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
        "PASSWORD": "Passw0rd!"
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
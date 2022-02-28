         ___        ______     ____ _                 _  ___  
        / \ \      / / ___|   / ___| | ___  _   _  __| |/ _ \ 
       / _ \ \ /\ / /\___ \  | |   | |/ _ \| | | |/ _` | (_) |
      / ___ \ V  V /  ___) | | |___| | (_) | |_| | (_| |\__, |
     /_/   \_\_/\_/  |____/   \____|_|\___/ \__,_|\__,_|  /_/ 
 ----------------------------------------------------------------- 


Hi there! Welcome to AWS Cloud9!

To get started, create some files, play with the terminal,
or visit https://docs.aws.amazon.com/console/cloud9/ for our documentation.

Happy coding!

https://step-functions-workshop.go-aws.com/
create IAM user with administrator access to the AWS account
acount id: xxx
user: xxx
pass: xxx
login url: https://xxx.signin.aws.amazon.com/console
CREATE A CLOUD9 WORKSPACE
TODO: Turn on AWS CloudTrail in your AWS account
https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html

# UNDERSTANDING SERVICE ORCHESTRATION
This approach works best when there is a higher level of coordination needed between services, often involving the need for robust retries, specific error handling, and optimized processing logic like conducting some steps in parallel or waiting for some steps to complete before continuing to execute a particular process.

# CREATE OUR EXAMPLE SERVICES
In this workshop, we’ll use the AWS Serverless Application Model (SAM) framework to help us write, deploy, and test the functionality in our services.
run scripts to 
use sam cli to build and deploy our git cloned lambda services

# CHECK

submit:
-------
aws lambda invoke --function-name sfn-workshop-SubmitApplication --payload '{ "name": "Spock", "address": "123 Enterprise Street" }' /dev/stdout 
response:
{"name":"Odedas","address":"13 Beit lehem Street","state":"SUBMITTED","id":"application_224aad0a-caf4-4550-9796-7f2189ad69df"}{
    "ExecutedVersion": "$LATEST", 
    "StatusCode": 200
}

find:
-----
aws lambda invoke --function-name sfn-workshop-FindApplications --payload '{ "state": "SUBMITTED" }' /dev/stdout


aws lambda invoke --function-name sfn-workshop-FlagApplication --payload '{ "id": "application_224aad0a-caf4-4550-9796-7f2189ad69df", "flagType": "REVIEW" }' /dev/stdout

flag as review:
----------------
aws lambda invoke --function-name sfn-workshop-FindApplications --payload '{ "state": "FLAGGED_FOR_REVIEW" }' /dev/stdout
response:
{"Items":[{"address":"13 Beit lehem Street","id":"application_224aad0a-caf4-4550-9796-7f2189ad69df","name":"Odedas","state":"FLAGGED_FOR_REVIEW"}],"Count":1,"ScannedCount":1}{
    "ExecutedVersion": "$LATEST", 
    "StatusCode": 200
}

approve:
--------
aws lambda invoke --function-name sfn-workshop-ApproveApplication --payload '{ "id": "application_224aad0a-caf4-4550-9796-7f2189ad69df" }' /dev/stdout
response:
{"address":"13 Beit lehem Street","id":"application_224aad0a-caf4-4550-9796-7f2189ad69df","name":"Odedas","state":"APPROVED"}{
    "ExecutedVersion": "$LATEST", 
    "StatusCode": 200
}

list approved:
-------------
aws lambda invoke --function-name sfn-workshop-FindApplications --payload '{ "state": "APPROVED" }' /dev/stdout
response:
{"Items":[{"address":"13 Beit lehem Street","id":"application_224aad0a-caf4-4550-9796-7f2189ad69df","name":"Odedas","state":"APPROVED"}],"Count":1,"ScannedCount":1}{
    "ExecutedVersion": "$LATEST", 
    "StatusCode": 200
}

Check a valid address
---------------------
aws lambda invoke --function-name sfn-workshop-DataChecking --payload '{"command": "CHECK_ADDRESS", "data": { "address": "123 Street" } }' /dev/stdout

aws lambda invoke --function-name sfn-workshop-DataChecking --payload '{"command": "CHECK_ADDRESS", "data": { "address": "DoesntMatchAddressPattern" } }' /dev/stdout
response flagged true->pass to human
{"flagged":true}{
    "ExecutedVersion": "$LATEST", 
    "StatusCode": 200
}

# ORCHESTRATION😎
## AWS Step Functions: 
    Built-in error handling, retrys..., 
    Execution event history, 
    seperate scenario to proccesses,
    visualize steps helps to understand, debug, 
    all other aws benefits like scale, pay etc... 
read more here
## https://step-functions-workshop.go-aws.com/40_our_first_orchestration/20_introducing_aws_step_functions.html
AWS Step Functions lets you coordinate services via fully-managed serverless workflows so you can build and update apps quickly. Workflows are made up of a series of steps, with the output of one step acting as input into the next. Application development is simpler and more intuitive using Step Functions because it translates your workflow into a state machine diagram that is easy to understand, easy to explain to others, and easy to change. You can monitor each of the steps of a workflow’s execution as they occur (and after as well), which helps you quickly identify problems and fix them. Step Functions automatically triggers and tracks each step, and can retry steps when there are errors, so your application workflow executes reliably, and in the order you expect.
## https://states-language.net/spec.html

# recup:
- Call a service and let Step Functions progress to the next state immediately after it gets an HTTP response.

- Call a service and have Step Functions wait for a job to complete.

- Call a service with a task token and have Step Functions wait until that token is returned along with a payload.

# Further Learning
https://step-functions-workshop.go-aws.com/70_wrapping_up/10_further_learning.html

Lambda
hello2 -> arn:aws:lambda:us-east-1:334940955711:function:hello2

name: test1
Function ARN: arn:aws:lambda:us-west-2:334940955711:function:test1

Role test1-role-aaq6chun 
Lambda will create an execution role named <myFunctionName>-role-aaq6chun, with permission to upload logs to Amazon CloudWatch Logs.

Configure a test event and tryit

invoke lambda with cli
aws lambda invoke --function-name arn:aws:lambda:us-west-2:334940955711:function:test1 --payload '{
  "key1": "value1",
  "key2": "value2",
  "key3": "value3"
}' /dev/stdout




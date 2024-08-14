# AWS Local Test

#### Install AWS SAM CLI

- Download and Install AWS SAM CLI [AWS Download SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html)
- Follow the AWS SAM CLI installation guide to install SAM CLI on your system.

- Verify the Installation
Run the following command to check if SAM CLI is installed correctly:
```bash
  sam --version
```

#### Download the Template

- Download the template.yml file for your Lambda function. If the downloaded file has a different name, rename it to template.yml.

#### Create the Event File

- Create a .json file that simulates the event that triggers the Lambda function.

#### Run Local Commands
- Run the following command to build the Lambda application:
```bash
  sam build
```
- Use the following command to invoke the Lambda function locally with the simulated event:
```bash
  sam local invoke LambdaName --event exampleEvent.json
```

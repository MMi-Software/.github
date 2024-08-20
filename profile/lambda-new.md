### Creating/Migrating Repositories

Steps to manually structure the update of a Lambda in AWS using a specific branch of the repository with GitHub Actions.

1. **Configure the GitHub Repository**:
   - Create a repository in GitHub using the prefix `lambda-`. Example: `lambda-mSand-ingestion`.
   - Push the local repository to the remote repository.

2. **Configure the Lambda in AWS**:
   - Create a Lambda function in AWS.
   - The Lambda function must be configured to be updated by uploading a new package (ZIP).

3. **Configure GitHub Actions**:
   - In the remote repository, create a workflow file (`.github/workflows/deploy.yml`).
   - This workflow will be triggered from the repository’s Actions console.

   Example of the dev job; it should be configured for each environment (change the lambda name `your-lambda-name-dev` to the one configured in AWS):
```yaml
name: Deploy Lambda

on:
  push:
    branches:
      - dev
      - qa
  pull_request:
    branches:
      - main
  workflow_dispatch: # Allows manual triggering of the workflow
    inputs:
      environment:
        description: 'Choose the environment to deploy'
        required: true
        default: 'dev'
        type: choice
        options:
          - dev
          - qa
          - prod

jobs:
  deploy-dev:
    if: github.ref == 'refs/heads/dev' || github.event_name == 'workflow_dispatch' && github.event.inputs.environment == 'dev'
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Set up Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '18'
      - name: Install dependencies
        run: npm install
      - name: Zip Lambda Function
        run: zip -r lambda.zip .
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2 #configure the necessary AWS credentials to interact with AWS services.
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID_DEV }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY_DEV }}
          aws-region: us-east-1
      - name: Deploy to AWS Lambda (Dev) #update the code of a Lambda function with the generated ZIP file.
        run: |
          aws lambda update-function-code \
            --function-name your-lambda-name-dev \
            --zip-file fileb://lambda.zip
```

4. **Configure Credentials in GitHub**:
   - Go to your repository settings in GitHub and add the AWS credentials (Access Key ID and Secret Access Key) in the "Secrets" section. This is done under "Settings" > "Secrets and variables" > "Actions".
   - Create two secrets: `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`, with the corresponding credentials, keeping in mind that the variables end with the environment: `DEV`, `QA`, or `PROD`.

5. **Test the Integration**:
   - Make a modification to the code. Push to the specified branch.
   - Use the Actions console to trigger the deploy.

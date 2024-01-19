# Serverless Node.js Application

## Overview

This repository contains the code and configuration files to set up a simple Node.js application using the Serverless Framework on AWS. The application uses Express.js to create a basic web server that responds with "Hello World!" when accessed.

## Prerequisites

Before getting started, ensure that you have Node.js and npm installed on your machine. Additionally, you'll need to install the Serverless Framework globally:

```bash
$ npm install -g serverless
If you are running this command on Linux, prefix it with sudo.
```

## Configure your AWS credentials using the following command:

```bash
$ sls config credentials \
  --provider aws \
  --key YOUR_PUBLIC_KEY \
  --secret YOUR_SECRET_KEY
```

- Replace YOUR_PUBLIC_KEY and YOUR_SECRET_KEY with your AWS IAM User's public and secret keys, ensuring the user has programmatic access and admin permissions.

## Getting Started
- Create a new folder for your Serverless Node.js application and navigate into it:
```bash
$ mkdir serverless-nodejs-app && cd serverless-nodejs-app
```
- Run the following command to generate the initial project structure using the Serverless Framework:
 ```bash
 $ sls create -t aws-nodejs -n serverless-nodejs-app

 ```
 ## Install the required dependencies (Express.js and serverless-http):

```bash
$ npm init -y
$ npm install --save express serverless-http

```

## Coding
- Rename the handler.js file to app.js.
- Replace the content of app.js with the following code:

```bash
// app.js
const express = require('express');
const sls = require('serverless-http');
const app = express();

app.get('/', async (req, res, next) => {
  res.status(200).send('Hello World!');
});

module.exports.server = sls(app);
```
- Update the serverless.yml file with the following configuration:
```bash
# serverless.yml
service: serverless-nodejs-app

provider:
  name: aws
  runtime: nodejs14.x
  stage: dev
  region: eu-central-1

functions:
  app:
    handler: app.server # reference the file and exported method
    events:
      - http:
          path: /
          method: ANY
          cors: true
      - http:
          path: /{proxy+}
          method: ANY
          cors: true
```

## Deployment
- To deploy your application, run the following command:

```bash
$ sls deploy
```
- The Serverless Framework will package your application, create a CloudFormation file based on serverless.yml, and deploy it to AWS. Once deployed, you'll receive an endpoint URL in the terminal.

- Visit the provided endpoint URL to access your deployed Serverless Node.js application

## This consolidated version should work well as a single README file for your project.

- Opening up the provided URL in a browser, you’ll see “Hello World!” get sent back to you.

## Deploying to Production!
- This is great and all, but not ready for a production environment… yet. Worry not! You’ll be surprised how simple it is to make it production-ready.

1. Add a secrets.json file for environment variables
For now, let’s just add the NODE_ENV in the secrets.json.

```bash
{
  "NODE_ENV": "production"
}
```

2. Add a reference for the secrets.json in the serverless.yml
As simple as adding the secrets file, it’s even easier to reference the file in the serverless.yml.

```bash
service: serverless-nodejs-app

custom: # add these two lines
  secrets: ${file(secrets.json)} # reference the secrets.json file

provider:
  name: aws
  runtime: nodejs14.x
  stage: production # make sure to change this to production
  region: eu-central-1
  environment: # add environment property
    NODE_ENV: ${self:custom.secrets.NODE_ENV} 
    # reference the NODE_ENV from the secrets.json file

functions:
  app:
    handler: app.server
    events:
      - http:
          path: /
          method: ANY
          cors: true
      - http:
          path: /{proxy+}
          method: ANY
          cors: true
```

- Amazing, that’s it! Delete the node_modules and .serverless folders from the service and run npm install again, but this time with the --production flag.

```bash
npm install --production
```

```bash
sls deploy
```
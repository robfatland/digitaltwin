# AWS

## Introduction

These notes are specific to composing the cloud element for a digital twin project on Amazon Web Services (AWS). 
Detailed instructions and notes follow in subsequent sections.

1. Establish an [AWS account](#aws_account) and sign in as an IAM User (not root)
2. On your development computer: [Build a Python environment zip file](#build-a-python-environment-zip-file)
3. Create a [Role](#create-a-role) with Policies for a Lambda function
4. Create a [Lambda function](#create-a-lambda-function) that assumes this role, incorporating the Python environment from above
5. Create an [API Gateway trigger](create-an-api-gateway-trigger) for the Lambda
6. Configure Twilio [credentials and code](configure-credentials-and-code) for the Lambda to use when it runs
7. Test [communication in both directions](test-communication-in-both-directions) 'cloud <--> IOT device' (Arduino in this case)
8. Add a [DynamoDB table](#add-a-dynamodb-table) to store data from the IOT device(s)
9. Add a non-IOT [API call](add-an-api-call) to the cloud environment


## AWS Account

## Build a Python environment zip file

## Create a Role

## Create a Lambda function

## Create an API Gateway trigger

## Configure credentials and code

## Test communication in both directions

## Add a DynamoDB table

* Create a DynamoDB table (See YouTube for walkthroughs)
    * Defaults except table name (digitaltwin) and partition key (messageid) > Create table
    * Note we have a resulting Amazon Resource Name (ARN: An unambiguous resource identifier)
        * This looks like **`arn:aws:dynamodb:us-west-2:123412341234:table/digitaltwin`**
* Create / modify the **`digitaltwin`** Role to include a DynamoDB policy
    * Console > IAM > Role > digitaltwin > Attach policies
    * Create a DynamoDB filter

## Add an API call

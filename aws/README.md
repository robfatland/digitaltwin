# AWS

## Introduction

These notes are specific to composing the cloud element for a digital twin project on Amazon Web Services (AWS). 
Detailed instructions and notes follow in subsequent sections.

1. Establish an [AWS account](#aws_account) and sign in as an IAM User (not root)
2. On your development computer: Build a Python environment zip file
3. Create a Role with Policies for a Lambda function
4. Create a Lambda function that assumes this role, incorporating the Python environment from above
5. Create an API Gateway trigger for the Lambda
6. Configure Twilio credentials and code for the Lambda to use when it runs
7. Test communication in both directions 'cloud <--> IOT device' (Arduino in this case)
8. Add a [DynamoDB table](#add-a-dynamodb-table) to store data from the IOT device(s)
9. Add a non-IOT API call to the cloud environment


## AWS Account

## Build a Python environment zip file

## Create a Role

## Create a Lambda function

## Create an API Gateway trigger

## Configure credentials and code

## Test communication in both directions

## Add a DynamoDB table

* Create a DynamoDB table (See YouTube for walkthroughs)
* and so on

## Add an API call

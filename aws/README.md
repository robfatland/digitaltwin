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

[Top](#aws)


- Test accounts are available with some pre-established credit limit (say $100 or so)
    - These can be used to become familiar with elements of AWS
- Paid accounts are billed monthly based on the resources we use
    - In academia we (UW) advocate for waiving indirect costs for cloud use
- The account root owner should be used to create an IAM User as an administrator (admin Policy)
    - The root account is not used for actual building / working on AWS
- Enable two-factor authentication and spend some time learning about secure operation on AWS
- Use great care to not place account information on GitHub, particularly not Access Keys


## Build a Python environment zip file


[Top](#aws)


This assumes you are working in a Linux environment. If you are working on a PC (Windows machine) 
this may involve a detour to install the most recent version of WSL or Anaconda; beyond the scope
of this document. 


The key idea here is to build a Lambda **layer** that will supplement the Lambda code and environment 
variables as described below.  A Lambda layer is a . zip file archive that can contain additional 
code or other content. A layer can contain libraries, a custom runtime, data, or configuration files.
In our case we are particularly interested in the *library* supporting access to the Twilio 
messaging service.

### Make the Lambda layer


Make a development folder in the Linux environment.


```
mkdir dtwin; cd dtwin
```


Here: Edit `requirements.txt`: 

```
twilio
pyjwt
jinja2
bcrypt
```

Create a sub-folder **`python`**:


```
mkdir python
```


Build the requirements into this folder:


```
pip install --target ./python -r ./requirements.txt
```


Zip the results


```
zip -FS -r ./python.zip ./python
```


Move this to a location accessible to an Upload wizard. In the case of working from a 
Linux environment on a Windows machine this requires crossing over to the Windows
filesystem, as in:


```
mv python.zip /mnt/c/Users/someusername/Downloads
```


We are now prepared to create an AWS Lambda Layer which exists independent of a
particular AWS Lambda function. It is subsequently added *to* the Lambda function
as we build *that*. 


- Log in to the AWS console > Set your region > Lambda > Layers > Create Layer
- Configure this wizard
    - Name: `digitaltwin_requirements`
    - Description: List the requirements.txt libraries `twilio` etcetera
    - Runtimes: Select ***ALL*** Python runtimes, not just one
    - Create



## Create a Role


[Top](#aws)




## Create a Lambda function


[Top](#aws)




### Add Layer


- Lambda function > Scroll to bottom of Code > Layers > Add Layer > Custom layers > Choose the layer added above





## Create an API Gateway trigger


[Top](#aws)




## Configure credentials and code


[Top](#aws)




### Environment variables


The Lambda will be triggered locally using its built-in **`Test`** button; or by an 
external entity via an API Gateway. This is covered below. 


- Lambda function > Configuration > Environment variables > Edit > Add two variables from the twilio account.
These are the User SID and the access token. They are loaded into program memory at the top of Lambda 
execution.


```
acct_id_env          123456789012345678901234567890123456789012345678901234567890
acct_token_env       ABCDEFGHIJKLMNOPQRABCDEFGHIJKLMNOPQRABCDEFGHIJKLMNOPQRABCDEFGHIJKLMNOPQR
```


These are loaded in code for example with: `acct_id_env = os.environment['acct_id_env']`.



### Test function


A Lambda function comes with a built-in test mechanism (see function tabs in the Lambda console). 
This makes use of a JSON specification of some key-value pairs so here we configure this to do
something of use in our overall process, specifically try and send a message to an IOT device.
The third parameter **`isIOT`** will be used to cause the Lambda to initiate a message without
regard to parsing inbound content from an IOT device. 


- Edit the test Event JSON read as follows:


```
{
  "testuser": "kilroy",
  "testdata": "3.14",
  "isIOT": "False"
}
```


- Save, Test


## Test communication in both directions


[Top](#aws)



## Add a DynamoDB table


[Top](#aws)


* Create a DynamoDB table (See YouTube for walkthroughs; 
[example](https://www.youtube.com/watch?v=9eHh946qTIk), [example](https://www.youtube.com/watch?v=2k2GINpO308),
[example](https://www.youtube.com/watch?v=Al1xwYhQ-BM)
    * Defaults except table name (digitaltwin) and partition key (messageid) > Create table
    * Note we have a resulting Amazon Resource Name (ARN: An unambiguous resource identifier)
        * This looks like **`arn:aws:dynamodb:us-west-2:123412341234:table/digitaltwin`**
* Create / modify the **`digitaltwin`** Role to include a DynamoDB policy
    * Console > IAM > Role > digitaltwin > Attach policies
    * Create a DynamoDB filter



## Add an API call


[Top](#aws)




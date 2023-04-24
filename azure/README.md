# Azure


## Introduction


These notes are specific to composing the cloud element for a digital twin project on the Microsoft Azure cloud.
Detailed instructions and notes follow in subsequent sections. 


There are three primary services in play here: 
* A serverless **`Azure Function`**
* A serverless **`CosmosDB`** NoSQL database (link) 
* An API endpoint visible to the outside world (link, brand name)


Here are the Azure-centric steps for the digital twin demonstrator: 


1. Establish an [Azure account](#azure-account) and sign in
2. On your development computer: [Build a Python environment zip file](#build-a-python-environment-zip-file)
3. Create an Azure Function App
4. Create an [Azure Function](#create-an-azure-function)
5. Create a [trigger](#trigger) for the Azure Function
6. Configure Twilio [credentials and code](#configure-credentials-and-code) for the Azure Function to use when it runs
7. Test [communication in both directions](#test-communication-in-both-directions) 'cloud <--> IOT device' (Arduino in this case)
8. Add a [CosmosDB container](#add-a-cosmosdb-container) to store data from the IOT device
9. Add a non-IOT [API call](#add-an-api-call) to the cloud environment


## Azure Account

[Top](#azure)


## Build a Python environment zip file


[Top](#azure)


This assumes you are working in a Linux environment. If you are working on a PC (Windows machine) 
this may involve a detour to install the most recent version of WSL or Anaconda; beyond the scope
of this document. 


The key idea here is to build a Lambda **layer** that will supplement the Lambda code and environment 
variables as described below.  A Lambda layer is a . zip file archive that can contain additional 
code or other content. A layer can contain libraries, a custom runtime, data, or configuration files.
In our case we are particularly interested in the *library* supporting access to the Twilio 
messaging service.

### Make the Azure Function layer


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


We are now prepared to create an Azure Function which exists independent of a
particular Azure Function. It is subsequently added *to* the Azure Function
as we build *that*. 


- Log in to the Azure portal > Set your region > Lambda > Layers > Create Layer
- Configure this wizard
    - Name: `digitaltwin_requirements`
    - Description: List the requirements.txt libraries `twilio` etcetera
    - Runtimes: Select ***ALL*** Python runtimes, not just one
    - Create



## Create a Role


[Top](#azure)




## Create a Lambda function


[Top](#azure)




### Add Layer


- Lambda function > Scroll to bottom of Code > Layers > Add Layer > Custom layers > Choose the layer added above





## Create an API Gateway trigger


[Top](#azure)




## Configure credentials and code


[Top](#azure)


This step emplaces Python code in the Event Handler code box. This code references Twilio credentials
by means of environment variables. While we are setting these up we also configure the Test utility 
in order to test the Lambda function without necessarily having to run the IOT device. 


### Code


The following code is not a 'finished result'. Rather it is used for testing the DynamoDB
connection (3 transactions), for parsing an Event using the Lambda design console 'Test' 
button, and for parsing simple messages from an IOT device. Corresponding code is 
indicated by flag comments. 

Note that near the top of the code four environment variables are read into program
memory using **`os.environ['key name']`**. These correspond to environment variables
set from the Configuration tab, `Environment variables` sub-section (left sidebar).


```
import base64                                # translate UDP message from the Arduino
from   datetime import datetime              # for timestamps .now() UTC
import boto3                                 # dynamodb resource connection
from boto3.dynamodb.conditions import Key    # supporting queries
import os                                    # access environment variable dictionary
import twilio.rest                           # interact with the twilio service

# possible future use: json, botocore.exceptions, decimal.Decimal

# authentication credentials for the twilio service account
acct_id_env = os.environ['acct_id_env']
acct_token_env = os.environ['acct_token_env']

# dynamodb table variables
table_name = os.environ['table_name']
action = os.environ['dynamodb_action']

def lambda_handler(event, context):

    dynamodb = boto3.resource("dynamodb")
    table = dynamodb.Table(table_name)
    
    ############################
    #
    # DynamoDB test section
    #
    ############################
    if action == 'get_item':
        # specifies both partition and sort keys
        response = table.get_item(Key={"message": "yyyyyyy", "timestamp": "2023-01-01 18:16:02.629135"})
        if "Item" in response: print(".get_item() returned Item:", response['Item'])
        else:                  print('No "Item" in get_item() response')
    elif action == 'put_item':
        newsum, now = 94.1234567, str(datetime.now())
        print(now, newsum)
        response = table.put_item(
            Item={"message": "yyyyyyy", "timestamp": now,
                  "data": str(3.14), "runsum": str(newsum), "otherthing": "Xtra"})
        if 'ResponseMetadata' in response: print(response)
        else:                              print('No "ResponseMetadata" in response.')
    elif action == 'query':
        # partition key only example: response = table.query(KeyConditionExpression=Key("message").eq("yyyyyyy"))
        # partition key and sort key example:
        response = table.query(KeyConditionExpression=Key("message").eq("yyyyyyy") & Key('timestamp').between('2023-01-01 18:14:45', '2023-01-01 18:16:36'))
        if "Items" in response: 
            for i in range(len(response["Items"])):
                print(response["Items"][i])
        else: print('No "Items" in query response.')
   
    ###########################
    #
    # Console Test Button: Event parsing
    #
    ###########################
    if 'isIOT' in event.keys() and not event['isIOT'] == 'True':                      # local Test
        data, user = float(event['testdata']), event['testuser']
        print('testuser ' + user + ' (not IOT) mentions some data: ' + '{:.9f}'.format(data))
        return { 'statusCode': 200 }
    
    ###########################
    #
    # IOT Device Message: Event parsing
    #
    ###########################
    elif 'queryStringParameters' in event.keys():                                     # inbound from IOT device
        print(str(event))                                                             # yaml > use https://codebeautify.org/yaml-pretty-print
        print(base64.b64decode(event["queryStringParameters"]["Payload"]).decode())
        this_sim = event["queryStringParameters"]["SimUniqueName"]
        client = twilio.rest.Client(acct_id_env, acct_token_env)
        result = client.supersim.v1.ip_commands.create(sim=this_sim, device_port=6969, payload="aye!")
        return { 'statusCode': 200, 'payload': str(event) }
```

### Environment variables


The Lambda will be triggered locally using its built-in **`Test`** button; or by an 
external IOT device via the API Gateway (see below). 


- Lambda function > Configuration > Environment variables > Edit > Add two variables from the twilio account.
These are the User SID and the access token. They are loaded into program memory at the top of Lambda 
execution.


```
acct_id_env          123456789012345678901234567890123456789012345678901234567890
acct_token_env       ABCDEFGHIJKLMNOPQRABCDEFGHIJKLMNOPQRABCDEFGHIJKLMNOPQRABCDEFGHIJKLMNOPQR
```

Add two more environment variables towards controlling the DynamoDB interaction of the Lambda function:

```
table_name           digitaltwin
dynamodb_action      put_item
```

For the latter: The code handles `put_item`, `get_item` and `query`. Anything else will just skip that part.
When running the Lambda in test mode the first few times: The idea would be to use `put_item` to place a 
few items into the table.


As noted above, environment variables are loaded into the Lambda program during execution.



### Lambda designer console Test utility


Under normal operation an IOT device sends a message to the Twilio service which in turn 
forwards it to the azure API Gateway which in turn forwards it to this Lambda function.
This is one way to test the Lambda function; but a much simpler test mechanism is built 
into azure Lambda functions, per the **`Test`** button. The Test tab in the Lambda design
console allows us to configure the **`event`** that is passed to the Lambda event handler. 


The Test event is configured using the JSON key-value table on the Test tab. Eventually
we use this Lambda trigger path to send a message to an IOT device.
The key **`isIOT`** has a boolean value for whether this Lambda trigger is
to be understood as 'from an IOT device'.


- Edit the test Event JSON table to read as follows:


```
{
  "testuser": "kilroy",
  "testdata": "3.14",
  "isIOT": "False"
}
```


- Save this and run the Test from the Code tab.
- If the Lambda code is modified, be sure to click **`Deploy`** before re-running **`Test`**.


## Test communication in both directions


[Top](#azure)


- On the IOT sensor IDE: Create a program or manual interface that sends a simple text payload to the Twilio service.
- Configure Twilio to refer these messages (organized by 'fleet') to the API Gateway
- One such message will create an **`event`** that is passed to the Lambda function
    - The execution of the Lambda will produce output in its log files
    - These log files can be accessed through the azure browser console
        - Lambda design console > Monitor tab > View logs in CloudWatch > choose most recent
- Once this sequence of events is established as working: Send a message from Lambda back to the IOT sensor
    - Both messages IOT > Lambda and Lambda > IOT should be present in Twilio's message logs
- The return message from Lambda > IOT should appear in the Serial connection window of the IDE
- Get the IOT sensor code running autonomously



## Add a DynamoDB table


[Top](#azure)


* [This documentation](https://docs.azure.amazon.com/amazondynamodb/latest/developerguide/GettingStarted.html) 
is a good reference on the azure serverless NoSQL database service 'DynamoDB'.
* Create a DynamoDB table (See YouTube for walkthroughs; 
[example](https://www.youtube.com/watch?v=9eHh946qTIk), [example](https://www.youtube.com/watch?v=2k2GINpO308),
[example](https://www.youtube.com/watch?v=Al1xwYhQ-BM)
    * azure Console > DynamoDB
        * Table name 'digitaltwin'
        * Partition key ('message')
        * Sort key ('timestamp')
        * Table settings > Customize Settings
            * Read / Write capacity settings > On-demand
    * Create table
        * Note we have a resulting Amazon Resource Name (ARN: An unambiguous resource identifier)
            * This looks like **`arn:azure:dynamodb:us-west-2:123412341234:table/digitaltwin`**
* Create / modify the **`digitaltwin`** Role used by the Lambda function: Add a DynamoDB policy
    * Console > IAM > Role > digitaltwin > Attach policies
    * Create a DynamoDB filter; find AmazonDynamoDBFullAccess Policy and attach this to the digitaltwin Role
* In the azure Console > DynamoDB > Tables > **`digitaltwin`** use **`Explore table items`** to review content
    * Partition keys correspond to independent memory blocks, each up to 10GB
    * Sort keys are more granular and { partition key + sort key } should be unique
    * In the azure Lambda function: **`import boto3`** enables working with this DynamoDB table as a programmatic resource
        * **`get_item()`** recovers information from the **`digitaltwin`** table
        * **`put_item()`** stores new data in this table
        * **`query()`** interrogates the table (zero or one or more results possible)


The basic pieces are in place at this point. Continuation work might include:


- Formalizing the data stream to DynamoDB
- Build equivalent functionality on Azure and GCP
- Construct an actuator scenario with directives sent from the cloud back to the IOT sensors
- Monitor operation over time to get a sense of system reliability


#### Aside: azure CLI


The **`boto3`** library is a very convenient means of interacting with the DynamoDB service. For testing and
debugging purposes one can also interact with DynamoDB using the azure command line interface (CLI). 
This is free to download and install on the development system; so it looks and runs like any other Linux utility.
An example command (after the CLI is configured with user credentials) is: 

```
az sts get-caller-identity
```

This verifies that we are who we claim to be (IAM User, correct account and so forth). Each cloud has such 
a CLI and each is a potentially powerful debugging tool. In the build narrative (recipe) presented here 
the CLI is not essential as we have the azure console, the Twilio interface and the IOT sensor IDE to work
with.



## Add an API call


[Top](#azure)


This section is intended to cover three important functions. 


- Debug the cloud data system functionality independent of the IOT Sensor devices
- Query the cloud data system (get data subsets) using code
- Check system status; also from code


While 'from code' is the goal we have a simpler approach on hand *first*. 


The 'wrap your mind around it' concept is that the API Gateway provides us with a URL; so we
should be able to begin using this via copy-paste-modify in a browser address bar. We want to
receive confirmation message from the Lambda (serverless) function in reply. 






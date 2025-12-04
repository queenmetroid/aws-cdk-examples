# AWS API Gateway HTTP API to AWS Lambda in VPC to DynamoDB CDK Python Sample!


## Overview

Creates an [AWS Lambda](https://aws.amazon.com/lambda/) function writing to [Amazon DynamoDB](https://aws.amazon.com/dynamodb/) and invoked by [Amazon API Gateway](https://aws.amazon.com/api-gateway/) REST API. 

![architecture](docs/architecture.png)

## Features

### Observability
- **AWS X-Ray Tracing**: End-to-end distributed tracing enabled across API Gateway, Lambda, and DynamoDB
- **CloudWatch Alarms**: Automated alerts for Lambda errors and performance issues
- **Custom Subsegments**: Detailed tracing of DynamoDB operations and business logic

### Security and Compliance
- **API Gateway Access Logs**: Comprehensive logging of all API requests with caller identity, IP, and response details
- **API Gateway Execution Logs**: Detailed operational logs for debugging and performance analysis
- **Lambda CloudWatch Logs**: 1-year retention policy for audit and compliance
- **VPC Flow Logs**: Network traffic monitoring for security investigations
- **DynamoDB Point-in-Time Recovery**: Continuous backups for data protection and audit trails

## Setup

The `cdk.json` file tells the CDK Toolkit how to execute your app.

This project is set up like a standard Python project.  The initialization
process also creates a virtualenv within this project, stored under the `.venv`
directory.  To create the virtualenv it assumes that there is a `python3`
(or `python` for Windows) executable in your path with access to the `venv`
package. If for any reason the automatic creation of the virtualenv fails,
you can create the virtualenv manually.

To manually create a virtualenv on MacOS and Linux:

```
$ python3 -m venv .venv
```

After the init process completes and the virtualenv is created, you can use the following
step to activate your virtualenv.

```
$ source .venv/bin/activate
```

If you are a Windows platform, you would activate the virtualenv like this:

```
% .venv\Scripts\activate.bat
```

Once the virtualenv is activated, you can install the required dependencies.

```
$ pip install -r requirements.txt
```

At this point you can now synthesize the CloudFormation template for this code.

```
$ cdk synth
```

To add additional dependencies, for example other CDK libraries, just add
them to your `setup.py` file and rerun the `pip install -r requirements.txt`
command.

## Deploy
At this point you can deploy the stack. 

Using the default profile

```
$ cdk deploy
```

With specific profile

```
$ cdk deploy --profile test
```

## After Deploy
Navigate to AWS API Gateway console and test the API with below sample data 
```json
{
    "year":"2023", 
    "title":"kkkg",
    "id":"12"
}
```

You should get below response 

```json
{"message": "Successfully inserted data!"}
```

## Monitoring and Observability

### Viewing X-Ray Traces
1. Navigate to AWS X-Ray console
2. Select "Service Map" to view the complete request flow: API Gateway → Lambda → DynamoDB
3. Select "Traces" to view individual request traces with detailed timing information
4. Use filters to find traces by annotation (e.g., `annotation.operation = "insert_custom_item"`)

### CloudWatch Alarms
The stack creates the following alarms:
- **LambdaErrorAlarm**: Triggers when Lambda errors exceed 5 in a single evaluation period
- **LambdaDurationAlarm**: Triggers when Lambda duration exceeds 60 seconds for 2 consecutive periods

## Security and Compliance

### Logging Configuration
This stack implements comprehensive logging for security investigations and compliance:

#### API Gateway Logs
- **Access Logs**: Captured in CloudWatch Logs with 1-year retention
  - Includes: caller identity, IP address, HTTP method, request time, response status
  - Query using CloudWatch Logs Insights
- **Execution Logs**: INFO level logging for detailed request/response debugging

#### Lambda Logs
- **CloudWatch Logs**: Automatic logging with 1-year retention policy
- Query logs using CloudWatch Logs Insights for security investigations

#### VPC Flow Logs
- **Network Traffic**: All traffic (ACCEPT and REJECT) logged to CloudWatch
- **Retention**: 1-year retention for security analysis
- Use for investigating network anomalies and security incidents

#### DynamoDB
- **Point-in-Time Recovery**: Enabled for continuous backups
- Allows recovery from accidental data modifications or deletions
- Provides audit trail for data changes

### CloudTrail Requirements
This stack requires AWS CloudTrail to be enabled at the account or organization level to log API calls for security audit purposes. Ensure CloudTrail is configured to capture:
- Management events for all services
- Data events for DynamoDB table operations
- Lambda function invocations

If CloudTrail is not configured at the account level, consider adding it to this stack or enabling it through AWS Organizations.

### Querying Logs for Security Investigations

**API Gateway Access Logs:**
```
fields @timestamp, ip, httpMethod, resourcePath, status
| filter status >= 400
| sort @timestamp desc
```

**Lambda Function Logs:**
```
fields @timestamp, @message
| filter @message like /ERROR/
| sort @timestamp desc
```

**VPC Flow Logs:**
```
fields @timestamp, srcAddr, dstAddr, srcPort, dstPort, action
| filter action = "REJECT"
| sort @timestamp desc
```

## Cleanup 
Run below script to delete AWS resources created by this sample stack.
```
cdk destroy
```

## Useful commands

 * `cdk ls`          list all stacks in the app
 * `cdk synth`       emits the synthesized CloudFormation template
 * `cdk deploy`      deploy this stack to your default AWS account/region
 * `cdk diff`        compare deployed stack with current state
 * `cdk docs`        open CDK documentation

Enjoy!

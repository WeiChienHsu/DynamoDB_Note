- [Module 1 Creating a Static Website in Amazon S3](#Module-1-Creating-a-Static-Website-in-Amazon-S3)
  - [Create an S3 Bucket](#Create-an-S3-Bucket)
    - [Good Bucket Names](#Good-Bucket-Names)
  - [Upload the Website Content to your S3 Bucket](#Upload-the-Website-Content-to-your-S3-Bucket)
  - [Update the S3 Bucket Policy](#Update-the-S3-Bucket-Policy)
    - [Remove public access](#Remove-public-access)
  - [Configure website Hosting](#Configure-website-Hosting)
- [Module 2: Creating a Service with AWS Fargate](#Module-2-Creating-a-Service-with-AWS-Fargate)
  - [Creating the Core Infrastructure using AWS CloudFormation](#Creating-the-Core-Infrastructure-using-AWS-CloudFormation)
  - [Deploying a Service with AWS Fargate](#Deploying-a-Service-with-AWS-Fargate)
  - [Configuring the Service Prerequisites in Amazon ECS](#Configuring-the-Service-Prerequisites-in-Amazon-ECS)
  - [Enabling a Load Balanced Fargate Service](#Enabling-a-Load-Balanced-Fargate-Service)
  - [Creating a Service with Fargate](#Creating-a-Service-with-Fargate)
  - [Update Mythical Mysfits to Call the NLB](#Update-Mythical-Mysfits-to-Call-the-NLB)
  - [Automating Deployments using AWS Code Services](#Automating-Deployments-using-AWS-Code-Services)
  - [Test the CI/CD Pipeline](#Test-the-CICD-Pipeline)
- [Module 3 Adding a Data Tier with Amazon DynamoDB](#Module-3-Adding-a-Data-Tier-with-Amazon-DynamoDB)
  - [Adding a NoSQL Database to Mythical Mysfits](#Adding-a-NoSQL-Database-to-Mythical-Mysfits)
  - [Changing the application to read from Amazon DynamoDB](#Changing-the-application-to-read-from-Amazon-DynamoDB)
- [Module 4: Adding User and API features with Amazon API Gateway and AWS Cognito](#Module-4-Adding-User-and-API-features-with-Amazon-API-Gateway-and-AWS-Cognito)
  - [Adding a User Pool for Website Users](#Adding-a-User-Pool-for-Website-Users)
  - [Adding a new REST API with Amazon API Gateway](#Adding-a-new-REST-API-with-Amazon-API-Gateway)
- [Module 5: Capturing User Behavior](#Module-5-Capturing-User-Behavior)
      - [An AWS Kinesis Data Firehose delivery stream](#An-AWS-Kinesis-Data-Firehose-delivery-stream)
      - [An Amazon S3 bucket](#An-Amazon-S3-bucket)
      - [An AWS Lambda function](#An-AWS-Lambda-function)
      - [An Amazon API Gateway REST API](#An-Amazon-API-Gateway-REST-API)
      - [IAM Roles](#IAM-Roles)


# Module 1 Creating a Static Website in Amazon S3

## Create an S3 Bucket

```
aws s3 mb s3://REPLACE_ME_BUCKET_NAME
```

- Observe the requirements for bucket names.
- - Bucket names must be unique across all existing bucket names in Amazon S3.

- - Bucket names must comply with DNS naming conventions.

- - Bucket names must be at least 3 and no more than 63 characters long.

- - Bucket names must not contain uppercase characters or underscores.

- - Bucket names must start with a lowercase letter or number.

- - Bucket names must be a series of one or more labels. Adjacent labels are separated by a single period (.). Bucket names can contain lowercase letters, numbers, and hyphens. Each label must start and end with a lowercase letter or a number.

- - Bucket names must not be formatted as an IP address (for example, 192.168.5.4).

### Good Bucket Names

- my-eu-bucket-3
- my-project-x
- 4my-group

---

## Upload the Website Content to your S3 Bucket

- Copy the initial page of the Mystical Misfits website (index.html) to your S3 bucket using the [aws s3 cp] command:


```
aws s3 cp ~/environment/aws-modern-application-workshop/module-1/web/index.html s3://REPLACE_ME_BUCKET_NAME/index.html
```

- Following the "principle of least privilege", access to buckets and objects is denied by default. 

- You can verify that issuing a GET request:

```
curl -I "https://REPLACE_ME_BUCKET_NAME.s3-$(aws configure get region).amazonaws.com/index.html"
HTTP/1.1 403 Forbidden
```

---

## Update the S3 Bucket Policy

- To serve as a public website, we can create an S3 Bucket Policy that indicates objects stored within this new bucket are publicly accessible. 

- S3 Bucket Policies are represented as JSON documents that `authorizes or denies the invocation of S3 Actions` (S3 API calls) to Principals (in our public example case, anyone).

- The JSON document for the necessary bucket policy is located at: `~/environment/aws-modern-application-workshop/module-1/aws-cli/website-bucket-policy.json.` 

- This file contains a string that needs to be replaced with the bucket name you've chosen (indicated with REPLACE_ME_BUCKET_NAME).

### Remove public access

```
aws s3api delete -bucket-policy --bucket REPLACE_ME_BUCKET_NAME 
```

---

## Configure website Hosting

- As you just verified, your bucket is ready to store and serve "objects" (a.k.a. files plus their metadata). 

- However, a bucket behaves differently from usual webservers. 
  
- For example, a GET request to the bucket HTTP endpoint would trigger a List Objects operation, instead of returning the root object. The bucket policy does not grant the s3:ListBucket, so this request is denied:

```
curl -I "https://REPLACE_ME_BUCKET_NAME.s3-$(aws configure get region).amazonaws.com"
HTTP/1.1 403 Forbidden
```

- Configuring static website hosting gives you a different endpoint that serves the specified index and error documents, and can evaluate redirection rules. Create a website configuration with the aws s3 website command:

```
aws s3 website s3://REPLACE_ME_BUCKET_NAME --index-document index.html
```

- Now you can use the website endpoint to serve static content directly from your S3 Bucket.

```
http://REPLACE_ME_BUCKET_NAME.s3-website-REPLACE_ME_YOUR_REGION.amazonaws.com
```

***

# Module 2: Creating a Service with AWS Fargate

In Module 2 you will `replace` the static list of misfits by a computed list obtained from a new microservice API hosted using AWS Fargate on Amazon Elastic Container Service.

AWS Fargate is a deployment option in Amazon ECS that allows you to deploy containers without having to manage any clusters or servers. 

For our Mythical Mysfits backend, we will use Java and create a `Spring Boot app in a Docker container behind a Network Load Balancer`. These will form the microservice backend for the frontend website to integrate with.

---

## Creating the Core Infrastructure using AWS CloudFormation



## Deploying a Service with AWS Fargate



## Configuring the Service Prerequisites in Amazon ECS



## Enabling a Load Balanced Fargate Service



## Creating a Service with Fargate



## Update Mythical Mysfits to Call the NLB


## Automating Deployments using AWS Code Services


## Test the CI/CD Pipeline



***

# Module 3 Adding a Data Tier with Amazon DynamoDB

- Now that you have a service deployed and a working CI/CD pipeline to deliver changes to that service automatically whenever you update your code repository, you can quickly move new application features from conception to available for your Mythical Mysfits customers.

- With this increased agility, let's add another foundational piece of functionality to the Mythical Mysfits website architecture, a data tier. 

- In this module you will `create a table in Amazon DynamoDB, a managed and scalable NoSQL database service on AWS with super fast performance`.

Rather than have all of the Mysfits be stored in a static JSON file, we will store them in a database to make the websites future more extensible and scalable.

---

## Adding a NoSQL Database to Mythical Mysfits


## Changing the application to read from Amazon DynamoDB

***

# Module 4: Adding User and API features with Amazon API Gateway and AWS Cognito

- In order to add `personalization` to the Mythical Mysfits website, like allowing users to vote for their favorite mysfit and adopt a mysfit, we need to first have users register and authenticate on the website.

- To enable registration and authentication of website users, we will create a User Pool in `AWS Cognito` - a fully managed user identity management service.

- Then, to make sure that only registered users are authorized to like or adopt mysfits on the website, we will deploy `an REST API with Amazon API Gateway as a facade to our Network Load Balancer (NLB)`. 
  
- Amazon API Gateway is also a managed service, and provides commonly required REST API capabilities out of the box like SSL termination, request authorization, throttling, API stages and versioning, and much more.

---

## Adding a User Pool for Website Users


## Adding a new REST API with Amazon API Gateway

***

# Module 5: Capturing User Behavior

- Now that your Mythical Mysfits site is up and running, let's create a way to better understand how users are interacting with the website and its Mysfits. 

- It would be very easy for us to analyze user actions taken on the website that lead to data changes in our backend - when mysfits are adopted or liked. 

- But understanding the actions your users are taking on the website before a decision to like or adopt a mysfit could help you design a better user experience in the future that leads to mysfits getting adopted even faster. 

- To help us gather these insights, we will implement the ability for the website frontend to submit a tiny request, each time a mysfit profile is clicked by a user, to a new microservice API we'll create.

- Those records will `be processed in real-time by a serverless code function, aggregated, and stored for any future analysis` that you may want to perform.

- Modern application design principles prefer focused, decoupled, and modular services. 

- So rather than add additional methods and capabilities within the existing Mysfits service that you have been working with so far, we will create a new and decoupled service for the purpose of receiving user click events from the Mysfits website. 

---

#### An AWS Kinesis Data Firehose delivery stream

- Kinesis Firehose is a highly available and managed real-time streaming service that `accepts data records` and `automatically ingests them into several possible storage destinations` within AWS, examples including an Amazon S3 bucket, or an Amazon Redshift data warehouse cluster. 
  
- Kinesis Firehose also enables all of the records received by the stream to be `automatically delivered to a serverless function created with AWS Lambda` 

- This means that code you've written can perform any additional processing or transformations of the records before they are aggregated and stored in the configured destination.

#### An Amazon S3 bucket

A new bucket will be created in S3 where all of the processed click event records are aggregated into files and stored as objects.

#### An AWS Lambda function

- AWS Lambda enables developers to write code functions that `only contain what their logic requires` and have their code be deployed, invoked, made highly reliable, and scale `without having to manage infrastructure` whatsoever. 
  
- Here, a Serverless code function is defined using `AWS SAM`. 
  
- It will be deployed to AWS Lambda, written in Java, and then `process and enrich the click records that are received by the delivery stream`. 
  
- The code we've written is very simple and the enriching it does could have `been accomplished on the website frontend without any subsequent processing` at all. 
  
- The function retrieves additional attributes about the clicked on Mysfit to make the click record more meaningful (data that was already retrieved by the website frontend). 
  
- But, for the purpose of this workshop, the code is meant to demonstrate the architectural possibilities of including a serverless code function to perform any additional processing or transformation required, in real-time, before records are stored. 
  
- Once the Lambda function is created and `the Kinesis Firehose delivery stream is configured as an event source for the function`, the delivery stream will `automatically deliver click records as events to code function we've created`, `receive the responses that our code returns`, and `deliver the updated records to the configured Amazon S3 bucket`.


#### An Amazon API Gateway REST API

- AWS Kinesis Firehose provides a service API just like other AWS services, and in this case we are using its `PutRecord` operation to put user click event records into the delivery stream. 

- But, we `don't want our website frontend to have to directly integrate with the Kinesis Firehose PutRecord API`. 
  
- Doing so would require us to manage AWS credentials within our frontend code `to authorize those API requests to the PutRecord API`, and it would `expose to users the direct AWS API that is being depended on`. 
  
- So instead, we will use Amazon `API Gateway` to create an `AWS Service Proxy to the PutRecord API of Kinesis Firehose`. 
  
- This allows us to `craft our own public RESTful endpoint that does not require AWS credential management on the frontend for requests`.
  
-  Also, we will use a request mapping template in API Gateway as well, which will let us define our own request payload structure that will restrict requests to our expected structure and then transform those well-formed requests into the structure that the Kinesis Firehose PutRecord API requires.

####  IAM Roles

- `Kinesis Firehose requires a service role that allows it to deliver received records as events to the created Lambda function` as well as the processed records to the destination S3 bucket. 
  
- The `Amazon API Gateway API also requires a new role` that permits the API to invoke the PutRecord API within Kinesis Firehose for each received API request.


***
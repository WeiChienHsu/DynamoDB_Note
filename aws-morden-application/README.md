- [Module 1 Creating a Static Website in Amazon S3](#Module-1-Creating-a-Static-Website-in-Amazon-S3)
  - [Create an S3 Bucket](#Create-an-S3-Bucket)
    - [Good Bucket Names](#Good-Bucket-Names)
  - [Upload the Website Content to your S3 Bucket](#Upload-the-Website-Content-to-your-S3-Bucket)
  - [Update the S3 Bucket Policy](#Update-the-S3-Bucket-Policy)
    - [Remove public access](#Remove-public-access)
  - [Configure website Hosting](#Configure-website-Hosting)
- [Module 2: Creating a Service with AWS Fargate](#Module-2-Creating-a-Service-with-AWS-Fargate)
  - [Creating the Core Infrastructure using AWS CloudFormation](#Creating-the-Core-Infrastructure-using-AWS-CloudFormation)
      - [An Amazon VPC](#An-Amazon-VPC)
      - [Two NAT Gateways](#Two-NAT-Gateways)
      - [A DynamoDB VPC Endpoint](#A-DynamoDB-VPC-Endpoint)
      - [A Security Group](#A-Security-Group)
      - [IAM Roles](#IAM-Roles)
    - [Build the Cloud Formation Stack](#Build-the-Cloud-Formation-Stack)
  - [Deploying a Service with AWS Fargate](#Deploying-a-Service-with-AWS-Fargate)
    - [Building A Docker Image](#Building-A-Docker-Image)
      - [Build the docker image](#Build-the-docker-image)
    - [Pushing the Docker Image to Amazon ECR](#Pushing-the-Docker-Image-to-Amazon-ECR)
  - [Configuring the Service Prerequisites in Amazon ECS](#Configuring-the-Service-Prerequisites-in-Amazon-ECS)
    - [Create an ECS Cluster](#Create-an-ECS-Cluster)
    - [Create an AWS CloudWatch Logs Group](#Create-an-AWS-CloudWatch-Logs-Group)
    - [Register an ECS Task Definition](#Register-an-ECS-Task-Definition)
  - [Enabling a Load Balanced Fargate Service](#Enabling-a-Load-Balanced-Fargate-Service)
    - [Create a Network Load Balancer](#Create-a-Network-Load-Balancer)
    - [Create a Load Balancer Target Group](#Create-a-Load-Balancer-Target-Group)
    - [Create a Load Balancer Listener](#Create-a-Load-Balancer-Listener)
  - [Creating a Service with Fargate](#Creating-a-Service-with-Fargate)
    - [Creating a Service Linked Role for ECS](#Creating-a-Service-Linked-Role-for-ECS)
    - [Create the Service](#Create-the-Service)
    - [Test the Service](#Test-the-Service)
  - [Update Mythical Mysfits to Call the NLB](#Update-Mythical-Mysfits-to-Call-the-NLB)
  - [Automating Deployments using AWS Code Services](#Automating-Deployments-using-AWS-Code-Services)
    - [Create a S3 Bucket for Pipeline Artifacts](#Create-a-S3-Bucket-for-Pipeline-Artifacts)
    - [Create a CodeCommit Repository](#Create-a-CodeCommit-Repository)
    - [Create a CodeBuild Project](#Create-a-CodeBuild-Project)
    - [Create a CodePipeline Pipeline](#Create-a-CodePipeline-Pipeline)
    - [Enable Automated Access to ECR Image Repository](#Enable-Automated-Access-to-ECR-Image-Repository)
  - [Test the CI/CD Pipeline](#Test-the-CICD-Pipeline)
    - [Using Git with AWS CodeCommit](#Using-Git-with-AWS-CodeCommit)
    - [Pushing a Code Change](#Pushing-a-Code-Change)
    - [](#)
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
      - [IAM Roles](#IAM-Roles-1)
  - [Copy the Streaming Service Code](#Copy-the-Streaming-Service-Code)
  - [Update the Lambda Function Package and Code](#Update-the-Lambda-Function-Package-and-Code)
  - [Creating the Streaming Service Stack](#Creating-the-Streaming-Service-Stack)
    - [Create an S3 Bucket for Lambda Function Code Packages](#Create-an-S3-Bucket-for-Lambda-Function-Code-Packages)
    - [Use the SAM CLI to Package your Code for Lambda](#Use-the-SAM-CLI-to-Package-your-Code-for-Lambda)
  - [Emmiting Mysfit Profile Clicks to the Stream](#Emmiting-Mysfit-Profile-Clicks-to-the-Stream)
    - [Update the Website Content](#Update-the-Website-Content)
    - [Push the New Site Version to S3](#Push-the-New-Site-Version-to-S3)


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

- `AWS CloudFormation` is a service that can programmatically provision AWS resources that you declare within JSON or YAML files called CloudFormation Templates, enabling the common best practice of Infrastructure as Code. 

- We have provided a CloudFormation template to create all of the necessary Network and Security resources in /module-2/cfn/core.yml. This template will create the following resources:


---

#### An Amazon VPC

- `a network environment` that contains four subnets (two public and two private) in the 10.0.0.0/16 private IP space, as well as all the needed Route Table configurations. 
  
- The subnets for this network are created in separate AWS Availability Zones (AZ) to enable high availability across multiple physical facilities in an AWS Region. Learn more about how AZs can help you achieve High Availability.

#### Two NAT Gateways 

(one for each public subnet, also panning multiple AZs) - `allows the containers we will eventually deploy into our private subnets to communicate out to the Internet to download necessary packages`, etc.

#### A DynamoDB VPC Endpoint

our microservice backend will eventually integrate with Amazon DynamoDB for persistence (as part of module 3).

#### A Security Group 

Allows your docker containers to receive traffic on port 8080 from the Internet through the Network Load Balancer.

#### IAM Roles

Identity and Access Management Roles are created. These will be used throughout the workshop to give AWS services or resources you create access to other AWS services like DynamoDB, S3, and more.

---

### Build the Cloud Formation Stack

To create these resources, run the following command in the Cloud9 terminal (will take ~10 minutes for stack to be created):

```
aws cloudformation create-stack --stack-name MythicalMysfitsCoreStack --capabilities CAPABILITY_NAMED_IAM --template-body file://~/environment/aws-modern-application-workshop/module-2/cfn/core.yml   
```

`ROLLBACK_COMPLETE`: mean that the create is failed since we have had some duplicated IAM.

---

## Deploying a Service with AWS Fargate


### Building A Docker Image

Let's build and store a docker container image containing the code and configuration required to run the Mythical Mysfits backend. This will be built as a microservice API created using Java and Spring Boot. We will build the docker container image within Cloud9 and then push it to the Amazon Elastic Container Registry, where it will be available to pull when we create our service using Fargate.

#### Build the docker image

```
cd ~/environment/aws-modern-application-workshop/module-2/app/servi
mvn clean install
```

build the docker image, this will use the file in the current directory called Dockerfile that tells Docker all of the instructions that should take place when the build command is executed. Replace the contents in and the {braces} below with the appropriate information from the account/region you're working in.

Once you have your Account ID, you are ready to build the docker image. You will tag the image when performing the build command (using -t) with a specific tag format so that the image can later be pushed to the Amazon Elastic Container Registry service.

```
cd ..
docker build . -t REPLACE_ME_ACCOUNT_ID.dkr.ecr.REPLACE_ME_REGION.amazonaws.com/mythicalmysfits/service:latest
```

You will see docker download and install all of the necessary dependency packages that our application needs, and output the tag for the built image. Copy the image tag for later reference. Below the example tag shown is: 

```
111111111111.dkr.ecr.us-east-1.amazonaws.com/mythicalmysfits/service:latest
```
---

### Pushing the Docker Image to Amazon ECR

With a successful test of our service locally, we're ready to create a container image repository in Amazon Elastic Container Registry (Amazon ECR) and push our image into it. In order to create the registry, run the following command, this creates a new repository in the default AWS ECR registry created for your account.


---

## Configuring the Service Prerequisites in Amazon ECS

### Create an ECS Cluster

With the service image image stored in the ECR repository we can proceed to deploy to a service hosted on Amazon ECS using AWS Fargate. The same service you tested locally via the terminal in Cloud9 as part of the last module will now be deployed in the cloud and publicly available behind a Network Load Balancer.

First, we will create a Cluster in the Amazon Elastic Container Service (ECS). In this context a "Cluster" is just a reference to the compute power for our containers because we will not be managing servers. AWS Fargate allows you to specify that your containers be deployed to a cluster without having to actually provision or manage any servers yourself.


### Create an AWS CloudWatch Logs Group

Next, we will create a new log group in AWS CloudWatch Logs. AWS CloudWatch Logs is a service for log collection and analysis. The logs that your container generates will automatically be pushed to AWS CloudWatch logs as part of this specific group. This is especially important when using AWS Fargate since you will not have access to the server infrastructure where your containers are running.

### Register an ECS Task Definition

Now that we have a cluster created and a log group defined for where our container logs will be pushed to, we're ready to register an ECS task definition. A task in ECS is a set of container images that should be executed. A task definition declares that set of containers and the resources and configuration those containers require. You will use the AWS CLI to create a new task definition for how your new container image should be scheduled to the ECS cluster we just created.

Once you have replaced the values in task-defintion.json and saved it. Execute the following command to register a new task definition in ECS:

```
aws ecs register-task-definition --cli-input-json file://~/environment/aws-modern-application-workshop/module-2/aws-cli/task-definition.json
```

---

## Enabling a Load Balanced Fargate Service

- With a new task definition registered, we're ready to provision the infrastructure needed in our service stack. 

- Rather than directly expose our service to the Internet, we will provision a Network Load Balancer (NLB) to sit in front of our service tier. 
  
- This would enable our frontend website code to communicate with a single DNS name while our backend service would be free to elastically scale in-and-out, in multiple Availability Zones, based on demand or if failures occur and new containers need to be provisioned.


### Create a Network Load Balancer

To provision a new NLB, execute the following CLI command in the Cloud9 terminal (retrieve the subnetIds from the CloudFormation output you saved):

```
aws elbv2 create-load-balancer --name mysfits-nlb --scheme internet-facing --type network --subnets REPLACE_ME_PUBLIC_SUBNET_ONE REPLACE_ME_PUBLIC_SUBNET_TWO > ~/environment/nlb-output.json
```

When this command has successfully completed, a new file will be created in your IDE called nlb-output.json. You will be using the DNSName, VpcId, and LoadBalancerArn in later steps.

### Create a Load Balancer Target Group

- Next, use the CLI to create an NLB target group. A target group allows AWS resources to register themselves as targets for requests that the load balancer receives to forward. 

- Our service containers will automatically register to this target so that they can receive traffic from the NLB when they are provisioned.

- This command includes one value that will need to be replaced, your `vpc-id` which can be found as a value within the earlier saved MythicalMysfitsCoreStack output returned by CloudFormation.

```
aws elbv2 create-target-group --name MythicalMysfits-TargetGroup --port 8080 --protocol TCP --target-type ip --vpc-id REPLACE_ME_VPC_ID --health-check-interval-seconds 10 --health-check-path / --health-check-protocol HTTP --healthy-threshold-count 3 --unhealthy-threshold-count 3 > opn
```


---

### Create a Load Balancer Listener

Next, use the CLI to create a load balancer listener for the NLB. This informs that load balancer that for requests received on a specific port, they should be forwarded to targets that have registered to the above target group. Be sure to replace the two indicated values with the appropriate ARN from the TargetGroup and the NLB that you saved from the previous steps:

```
aws elbv2 create-listener --default-actions TargetGroupArn=REPLACE_ME_NLB_TARGET_GROUP_ARN,Type=forward --load-balancer-arn REPLACE_ME_NLB_ARN --port 80 --protocol TCP

```

---

## Creating a Service with Fargate

### Creating a Service Linked Role for ECS

If you have already used ECS in the past, you already have created a Service Linked Role and can skip over this step and move on to the next one. If you have never used ECS before, we need to create an service linked role in IAM that grants the ECS service itself permissions to make ECS API requests within your account. This is required because when you create a service in ECS, the service will call APIs within your account to perform actions like pulling docker images, creating new tasks, etc.


### Create the Service

- With the NLB created and configured, and the ECS service granted appropriate permissions, we're ready to create the actual ECS service where our containers will run and register themselves to the load balancer to receive traffic.


- We have included a JSON file for the CLI input that is located at: `~/environment/aws-modern-application-workshop/module-2/aws-cli/service-definition.json`. 

- This file includes all of the configuration details for the service to be created, including indicating that this service should be launched with AWS Fargate

- which means that `you do not have to provision any servers within the targeted cluster`. 
  
- The containers that are scheduled as part of the task used in this service will run on top of a cluster that is fully managed by AWS.

### Test the Service

Copy the DNS name you saved when creating the NLB and send a request to it using the preview browser in Cloud9 (or by simply any web browser, since this time our service is available on the Internet). Try sending a request to the mysfits resource:

```
#Replace with your NLB DNS name
http://mysfits-nlb-123456789-abc123456.elb.us-east-1.amazonaws.com/mysfits
```

---

## Update Mythical Mysfits to Call the NLB


Next, we need to integrate our website with your new API backend instead of using the hard coded data that we previously uploaded to S3. You'll need to update the following file to use the same NLB URL for API calls: `/module-2/web/index.html`


```html
var mysfitsApiEndPoint = "NLB URI"
```

---

## Automating Deployments using AWS Code Services

### Create a S3 Bucket for Pipeline Artifacts

- Now that you have a service up and running, you may think of code changes that you'd like to make to your service. 

- Instead of deploying manually, let's create a fully managed CI/CD stack that will automatically deliver all of the code changes that you make to your code base to the service you created during the last module.

- Similarly to the website bucket, this bucket needs a bucket policy to define access permissions. 
  
- But unlike our website bucket that allowed access to anyone, `only our CI/CD pipeline should have access` to this bucket. 
  
- We have provided the JSON file needed for this policy at `~/environment/aws-modern-application-workshop/module-2/aws-cli/artifacts-bucket-policy.json.` 
  
- Open this file, and inside you will need to replace several strings to include the ARNs that were created as part of the MythicalMysfitsCoreStack earlier, as well as your newly chosen bucket name for your CI/CD artifacts.

### Create a CodeCommit Repository

You'll need a repository to store and share your code. 

### Create a CodeBuild Project

- With a repository to store our code in, and an S3 bucket that will be used for our CI/CD artifacts, lets add to the CI/CD stack with a way for a service build to occur. 
  
- This will be accomplished by creating an AWS CodeBuild Project. 
  
- Any time a build execution is triggered, AWS CodeBuild will automatically provision a build server to our configuration and execute the steps required to build our docker image and push a new version of it to the ECR repository we created (and then spin the server down when the build is completed). 
  
- The steps for our build (which package our Java code and build/push the Docker container) are included in the `~/environment/aws-modern-application-workshop/module-2/app/buildspec.yml` file. 
  
- The buildspec.yml file is what you create to instruct CodeBuild what steps are required for a build execution within a CodeBuild project.

- To create the CodeBuild project, `another CLI input file is required to be updated with parameters specific to your resources`. 
  
- It is located at `~/environment/aws-modern-application-workshop/module-2/aws-cli/code-build-project.json`. 
  
- Similarly replace the values within this file as you have done before from the MythicalMysfitsCoreStackOutput. Once saved, execute the following with the CLI to create the project:

```
aws codebuild create-project --cli-input-json file://~/environment/aws-modern-application-workshop/module-2/aws-cli/code-build-project.json
```

### Create a CodePipeline Pipeline

- Finally, we need a way to continuously integrate our CodeCommit repository with our CodeBuild project so that builds will automatically occur whenever a code change is pushed to the repository. 

- Then, we need a way to continuously deliver those newly built artifacts to our service in ECS. AWS CodePipeline is the service that glues these actions together in a pipeline you will create next.

- Your pipeline in CodePipeline will do just what I described above. 
  
- Anytime `a code change is pushed into your CodeCommit repository`, `CodePipeline will deliver the latest code to your AWS CodeBuild project so that a build will occur`. 
  
- When successfully built by CodeBuild, CodePipeline will perform a deployment to ECS using the latest container image that the CodeBuild execution pushed into ECR.

```
aws codepipeline create-pipeline --cli-input-json file://~/environment/aws-modern-application-workshop/module-2/aws-cli/code-pipeline.json
```

### Enable Automated Access to ECR Image Repository

- We have one final step before our CI/CD pipeline can execute end-to-end successfully. 
  
- With a CI/CD pipeline in place, you won't be manually pushing container images into ECR anymore.
  
-  CodeBuild will be pushing new images now. 
  
-  We need to `give CodeBuild permission to perform actions on your image repository with an ECR repository policy`*. 
  
-  The policy document needs to be updated with the specific ARN for the CodeBuild role created by the MythicalMysfitsCoreStack, and the policy document is located at `~/environment/aws-modern-application-workshop/module-2/aws-cli/ecr-policy.json.` 
  
-  Update and save this file and then run the following command to create the policy:

```
aws ecr set-repository-policy --repository-name mythicalmysfits/service --policy-text file://~/environment/aws-modern-application-workshop/module-2/aws-cli/ecr-policy.json
```

---

## Test the CI/CD Pipeline

### Using Git with AWS CodeCommit

To test out the new pipeline, we need to configure git within your Cloud9 IDE and integrate it with your CodeCommit repository.

AWS CodeCommit provides a credential helper for git that we will use to make integration easy. 

### Pushing a Code Change

- After the change is pushed into the repository, you can open the CodePipeline service in the AWS Console to view your changes as they progress through the CI/CD pipeline. 

- After committing your code change, it will take about 5 to 10 minutes for the changes to be deployed to your live service running in Fargate. 
  
- During this time:
  
- 1. AWS CodePipeline will orchestrate triggering a pipeline execution when the changes have been checked into your CodeCommit repository
  
- 2. Trigger your CodeBuild project to initiate a new build
  
- 3. Retrieve the docker image that was pushed to ECR by CodeBuild
  
- 4. Perform an automated ECS Update Service action to connection drain the existing containers that are running in your service 
  
- 5. Replace them with the newly built image. 
  

- You can view the progress of your code change through the CodePipeline console here (no actions needed, just watch the automation in action!): AWS CodePipeline


### 


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

---

## Copy the Streaming Service Code

---

## Update the Lambda Function Package and Code

---

## Creating the Streaming Service Stack

### Create an S3 Bucket for Lambda Function Code Packages

### Use the SAM CLI to Package your Code for Lambda


---

## Emmiting Mysfit Profile Clicks to the Stream
 
### Update the Website Content

### Push the New Site Version to S3

To view the records that have been processed, they will arrive in the destination S3 bucket created as part of your MythicalMysfitsStreamingStack. Visit the S3 console here and explore the bucket you created for the streaming records (it will be prefixed with mythicalmysfitsstreamings-clicksdestinationbucket)

***
- [Module 1 Creating a Static Website in Amazon S3](#Module-1-Creating-a-Static-Website-in-Amazon-S3)
  - [Create an S3 Bucket](#Create-an-S3-Bucket)
    - [Good Bucket Names](#Good-Bucket-Names)
  - [Upload the Website Content to your S3 Bucket](#Upload-the-Website-Content-to-your-S3-Bucket)
  - [Update the S3 Bucket Policy](#Update-the-S3-Bucket-Policy)
    - [Remove public access](#Remove-public-access)
  - [Configure website Hosting](#Configure-website-Hosting)
- [Module 2: Creating a Service with AWS Fargate](#Module-2-Creating-a-Service-with-AWS-Fargate)
- [Module 3 - Adding a Data Tier with Amazon DynamoDB](#Module-3---Adding-a-Data-Tier-with-Amazon-DynamoDB)
- [Module 4: Adding User and API features with Amazon API Gateway and AWS Cognito](#Module-4-Adding-User-and-API-features-with-Amazon-API-Gateway-and-AWS-Cognito)
- [Module 5: Capturing User Behavior](#Module-5-Capturing-User-Behavior)


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

# Module 3 - Adding a Data Tier with Amazon DynamoDB

# Module 4: Adding User and API features with Amazon API Gateway and AWS Cognito


# Module 5: Capturing User Behavior


#
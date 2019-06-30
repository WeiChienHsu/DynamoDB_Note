# Creating a Static Website in Amazon S3

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



---
## Configure website Hosting

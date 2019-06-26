# DynamoDB

***

# AWS Lambda

- AWS Lambda lets you run code without provisioning or managing servers. You pay only for the compute time you consume - there is no charge when your code is not running.

- With Lambda, you can run code for virtually any type of application or backend service - all with zero administration. Just upload your code and Lambda takes care of everything required to run and scale your code with high availability. You can set up your code to automatically trigger from other AWS services or call it directly from any web or mobile app.

---

## Lambda Use Cases - Data processing

 
### Real-time file processing

You can use Amazon S3 to trigger AWS Lambda to process data `immediately after an upload`. For example, you can use Lambda to thumbnail images, transcode videos, index files, process logs, validate content, and aggregate and filter data in real-time.


![RealTimeStreamProcessing](./images/RealTimeFileProcess.png)


#### Examples

- The Seattle Times uses AWS Lambda to resize images for viewing on different devices such as desktop computers, tablets, and smartphones.

- Square Enix's corporate philosophy is to spread happiness across the globe by providing unforgettable experiences. The company provides high-quality entertainment content, services, and products. Its flagship video-game titles include Dragon Quest, Final Fantasy, and Tomb Raider.

### Real-time stream processing

You can use AWS Lambda and Amazon Kinesis to process real-time streaming data for application activity tracking, transaction order processing, click stream analysis, data cleansing, metrics generation, log filtering, indexing, social media analysis, and IoT device data telemetry and metering.

![RealTimeStreamProcessing](./images/RealTimeStreamProcessing.png)

#### Examples

- Localytics processes billions of data points in real-time, and uses Lambda to process historical and live data stored in S3 or streamed from Kinesis.

### Extract, transform, load

You can use AWS Lambda to perform data validation, filtering, sorting, or other transformations for every data change in a DynamoDB table and load the transformed data to another data store.

![ETLprocess](./images/ETLprocess.png)

#### Example

Zillow uses Lambda and Kinesis to track a subset of mobile metrics in realtime. With Kinesis and Lambda, we were able to develop and deploy a cost effective solution in two weeks.

---

## Introducing AWS Lambda functions

- The code you run on AWS Lambda is called a “Lambda function.” 

- After you create your Lambda function it is always ready to run as soon as it is `triggered`, similar to a formula in a spreadsheet. 
  
- Each function includes your code as well as some associated configuration information, including the `function name` and `resource requirements`. 
  
- Lambda functions are `“stateless`,” with no affinity to the underlying infrastructure, so that Lambda can rapidly launch as many copies of the function as needed to scale to the rate of incoming events.

- After you upload your code to AWS Lambda, you can associate your function with specific AWS resources (e.g. a particular Amazon S3 bucket, Amazon DynamoDB table, Amazon Kinesis stream, or Amazon SNS notification). 
  
- Then, when the resource changes, Lambda will execute your function and manage the compute resources as needed in order to keep up with incoming requests.


***

# AWS Kinesis Data Firehose

- Amazon Kinesis Data Firehose is the easiest way to reliably load streaming data into data lakes, data stores and analytics tools. 

- It can capture, transform, and load streaming data into Amazon S3, Amazon Redshift, Amazon Elasticsearch Service, and Splunk, enabling near real-time analytics with existing business intelligence tools and dashboards you’re already using today. 

- It is a fully managed service that automatically scales to match the throughput of your data and requires no ongoing administration. 
  
- It can also batch, compress, transform, and encrypt the data before loading it, minimizing the amount of storage used at the destination and increasing security.

- You can easily create a Firehose delivery stream from the AWS Management Console, configure it with a few clicks, and start sending data to the stream from hundreds of thousands of data sources to be loaded continuously to AWS – all in just a few minutes. 

- You can also configure your delivery stream to automatically convert the incoming data to columnar formats like Apache Parquet and Apache ORC, before the data is delivered to Amazon S3, for cost-effective storage and analytics.

- With Kinesis Data Firehose, you only pay for the amount of data you transmit through the service, and if applicable, for data format conversion. There is no minimum fee or setup cost.


---

## Kinesis Data Firehose - Data Flow

- For Amazon S3 destinations, streaming data is delivered to your S3 bucket. If data transformation is enabled, you can optionally `back up source data` to another Amazon S3 bucket.


- For Amazon Redshift destinations, streaming data is `delivered to your S3 bucket first`. Kinesis Data Firehose then issues an Amazon Redshift COPY command to load data from your S3 bucket to your Amazon Redshift cluster. If data transformation is enabled, you can optionally back up source data to another Amazon S3 bucket.


- For Amazon ES destinations, streaming data is delivered to your Amazon ES cluster, and it can optionally be backed up to your S3 bucket concurrently.

- For Splunk destinations, streaming data is delivered to Splunk, and it can optionally be backed up to your S3 bucket concurrently.

---



***
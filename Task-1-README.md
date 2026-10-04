# Task 1 – S3 Event Trigger with AWS Lambda and CloudWatch

## 📌 Objective

Create an event-driven AWS workflow where uploading an image to an Amazon S3 bucket triggers an AWS Lambda function, and the Lambda execution logs are monitored using Amazon CloudWatch.

## 🏗️ Architecture

```text
User
  │
  │ Upload Image
  ▼
Amazon S3 Bucket
  │
  │ S3 Event Trigger
  ▼
AWS Lambda
  │
  │ Execution Logs
  ▼
Amazon CloudWatch Logs
```

## 🛠️ AWS Services Used

- Amazon S3
- AWS Lambda
- Amazon CloudWatch
- AWS IAM

## ⚙️ Implementation Steps

### 1. Create an S3 Bucket

1. Open the AWS Management Console.
2. Go to **S3**.
3. Create a bucket.
4. Upload an image file to the bucket.

### 2. Create the Lambda Function

Create a Lambda function named:

```text
ProcessS3Image
```

Runtime:

```text
Python 3.x
```

Lambda code:

```python
import json

def lambda_handler(event, context):

    print("S3 Image Upload Event Received")

    print("Full Event:")
    print(json.dumps(event))

    bucket_name = event['Records'][0]['s3']['bucket']['name']
    object_key = event['Records'][0]['s3']['object']['key']
    object_size = event['Records'][0]['s3']['object']['size']

    print("Bucket Name:", bucket_name)
    print("Image Name:", object_key)
    print("Image Size:", object_size, "bytes")

    return {
        'statusCode': 200,
        'body': 'Image information processed successfully'
    }
```

### 3. Configure the S3 Trigger

Add an S3 trigger to the Lambda function.

The trigger is configured so that an image upload to the S3 bucket invokes the Lambda function.

### 4. Test the Workflow

Upload an image to the S3 bucket.

The upload generates an S3 event, which invokes the Lambda function.

### 5. Verify CloudWatch Logs

Open:

**CloudWatch → Log groups → `/aws/lambda/ProcessS3Image`**

Verify that the Lambda execution logs contain information such as:

- S3 bucket name
- Uploaded image name
- Image size
- S3 event information

## 📸 Screenshots

Add the following screenshots to this README:

```text
screenshots/task1/
├── 01-s3-bucket.png
├── 02-uploaded-image.png
├── 03-lambda-function.png
├── 04-s3-trigger.png
└── 05-cloudwatch-logs.png
```

> Replace the filenames above with the actual screenshot filenames used in the repository.

## ✅ Result

The S3 → Lambda → CloudWatch event-driven workflow was successfully implemented.

When an image is uploaded to S3, the S3 event triggers the Lambda function, and the Lambda execution details are available in CloudWatch Logs.

## 📚 Key Learnings

- Creating and using an Amazon S3 bucket
- Configuring S3 event notifications
- Creating an AWS Lambda function
- Reading S3 event data in Python
- Monitoring Lambda executions using CloudWatch
- Understanding event-driven serverless architecture

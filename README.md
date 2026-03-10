# 🚀 Automated CI/CD Pipeline & Serverless Backend Architecture

## 📌 Project Overview
This repository demonstrates a fully automated Continuous Integration and Continuous Deployment (CI/CD) pipeline integrated with a modern **Serverless Backend**. It bridges the gap between version control and cloud hosting by automatically deploying a static website to an **AWS S3 Bucket** whenever new code is pushed to the `main` branch. 

Furthermore, it features a dynamic **Visitor Counter** built entirely on AWS Serverless technologies (Lambda, DynamoDB, API Gateway) without provisioning a single server.

The goal of this project is to showcase modern **DevOps practices**, decoupled architecture, infrastructure security, and serverless compute capabilities.

---

## 🏗️ Architecture & Workflow
The architecture is divided into two decoupled systems working in harmony:

### 1. Frontend CI/CD Pipeline (The Vitrine)
1. **Developer Action:** Code is updated and pushed to the GitHub repository.
2. **Trigger:** GitHub Actions detects the push event.
3. **Authentication:** The workflow securely authenticates with AWS using encrypted GitHub Secrets.
4. **Deployment:** The AWS CLI syncs the updated repository files directly to the S3 bucket.

### 2. Serverless Backend (The Brain & Memory)
1. **Trigger:** A user visits the S3-hosted static website.
2. **API Call:** A lightweight JavaScript `fetch` function calls the **AWS API Gateway**.
3. **Compute:** The API triggers an **AWS Lambda** function (Python).
4. **Database:** The Lambda function securely updates and retrieves the visitor count from an **Amazon DynamoDB** NoSQL table.
5. **Response:** The updated count is displayed on the website in real-time.

---

## 🛠️ Technologies Used
- **Frontend & Hosting:** HTML5/CSS, JS, AWS S3 (Static Website Hosting)
- **CI/CD & Automation:** GitHub Actions, Git, AWS CLI
- **Serverless Backend:** AWS Lambda (Python 3.12), Amazon DynamoDB, Amazon API Gateway
- **Security & Access:** AWS IAM (Roles, Policies, Least Privilege), CORS, GitHub Secrets

---

## 🚀 How to Replicate This Architecture (Step-by-Step Guide)

### Part 1: CI/CD & Frontend Deployment

#### Phase 1: Create and Configure the AWS S3 Bucket
1. Navigate to the **S3** service in the AWS Console.
2. Create a globally unique bucket, uncheck "Block all public access", and save.
3. Go to the **Properties** tab, enable **Static website hosting**, and specify `index.html`.

#### Phase 2: Apply the S3 Bucket Policy
1. Go to the **Permissions** tab and edit the **Bucket policy**.
2. Paste the following JSON policy *(replace `YOUR-BUCKET-NAME`)*:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*"
        }
    ]
}
```

#### Phase 3: Create an AWS IAM User for GitHub Actions
1. Navigate to **IAM -> Users -> Create user** (e.g., `github-actions-bot`).
2. Attach the `AmazonS3FullAccess` policy directly.
3. Create an **Access key** (CLI format) and save the **Access Key ID** and **Secret Access Key**.

#### Phase 4: Configure GitHub Secrets & Workflow
1. In your GitHub repository, go to **Settings -> Secrets and variables -> Actions**.
2. Add `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`.
3. Update the `aws-region` and `s3://YOUR-BUCKET-NAME` in your `.github/workflows/deploy.yml` file.

---

### Part 2: The Serverless Backend (Visitor Counter)

#### Phase 5: Create the DynamoDB Table (The Memory)
1. Navigate to **DynamoDB** and click **Create table**.
2. Name it `PortfolioCounter` with a **Partition key** named `id` (String).
3. Once created, click **Explore table items -> Create item**.
4. Set the `id` to `view_count`, add a new **Number** attribute called `Counter`, and set its value to `0`.

#### Phase 6: Create the AWS Lambda Function (The Brain)
1. Navigate to **Lambda** and create a new function (**Author from scratch**).
2. Name it `VisitorCounter` and select **Python 3.12** as the runtime.
3. Go to **Configuration -> Permissions**, click the **Execution Role** link, and attach the `AmazonDynamoDBFullAccess` policy.
4. Paste the following Python code:
```python
import json
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('PortfolioCounter')

def lambda_handler(event, context):
    try:
        response = table.update_item(
            Key={'id': 'view_count'},
            UpdateExpression='ADD #c :incr',
            ExpressionAttributeNames={'#c': 'Counter'},
            ExpressionAttributeValues={':incr': 1},
            ReturnValues="UPDATED_NEW"
        )
        return {
            'statusCode': 200,
            'headers': {
                'Access-Control-Allow-Origin': '*',
                'Access-Control-Allow-Headers': '*',
                'Access-Control-Allow-Credentials': True
            },
            'body': json.dumps({'visit_count': int(response['Attributes']['Counter'])})
        }
    except Exception as e:
        return {'statusCode': 500, 'body': json.dumps(f"Error: {str(e)}")}
```

#### Phase 7: Expose via API Gateway (The Bridge)
1. In your Lambda function overview, click **+ Add trigger**.
2. Select **API Gateway -> Create a new API -> HTTP API**.
3. Set **Security** to `Open` and under **Additional settings**, enable **CORS**.
4. Copy the generated **API endpoint URL**.

#### Phase 8: Connect Frontend to Backend
1. Add the following JavaScript `fetch` function to your `index.html`:
```javascript
async function updateVisitorCount() {
    const response = await fetch('YOUR_API_GATEWAY_URL_HERE');
    const data = await response.json();
    document.getElementById('visitorCount').innerText = 'Visitors: ' + data.visit_count;
}
updateVisitorCount();
```

2. Commit and push — GitHub Actions will deploy your fully functional serverless architecture. ✨

---

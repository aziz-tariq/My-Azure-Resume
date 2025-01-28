# My-Azure-Resume
My own azure resume, following ACG Project.

AWS Resume Challenge - Static Website Hosting with Visitor Counter
This project implements the Azure Resume Challenge using AWS services to host a static resume website. It demonstrates how to build a secure, scalable, and globally accessible website with a visitor counter functionality. Below, you’ll find the architecture design, step-by-step implementation, and details about the technologies used.

Architecture Diagram
plaintext
Copy
Edit
┌────────────┐             ┌────────────┐
│  User      │             │  Route 53  │
│ (Browser)  │             │ (DNS)      │
└─────┬──────┘             └─────┬──────┘
      │                           │
      │                           ▼
      │                    Subdomain: resume.tariqaziz.site
      │                           │
      ▼                           │
┌────────────┐             ┌───────────────┐
│ CloudFront │◄────────────┤  ACM (SSL)    │
│ (CDN)      │             └───────────────┘
└─────┬──────┘
      │
      ▼
┌───────────────┐
│ S3 Bucket      │
│ (Static Files) │
└─────┬─────────┘
      │
      ▼
┌───────────────┐
│ Lambda         │
│ (Visitor Logic)│
└─────┬─────────┘
      │
      ▼
┌───────────────┐
│ DynamoDB       │
│ (Visitor Count)│
└───────────────┘
Features
Static Website Hosting: The resume is hosted on an S3 bucket, optimized with CloudFront for low latency.
Custom Subdomain: The subdomain resume.tariqaziz.site was registered through GoDaddy and configured in Route 53 for DNS management.
Secure Connection: A wildcard SSL certificate from ACM ensures all traffic is encrypted.
Visitor Counter: Tracks website visits in real time using AWS Lambda and DynamoDB.
Step-by-Step Implementation
Step 1: Create the S3 Bucket
Setup Static Website Hosting:

Create an S3 bucket (e.g., resume-static-site).
Enable static website hosting.
Upload your index.html and any other required files.
Set Bucket Permissions:

Ensure public read access for the bucket content.
Use bucket policy to grant s3:GetObject access for CloudFront.
Step 2: Configure CloudFront
Create a CloudFront Distribution:

Configure the origin to point to your S3 bucket.
Set the Default Root Object to index.html.
Enable ACM SSL/TLS Certificate for HTTPS support.
Set the Viewer Protocol Policy to Redirect HTTP to HTTPS.
Invalidate Cache:

After updating the website, invalidate the cache to reflect changes immediately.
Step 3: Register a Domain with GoDaddy
Purchase a Domain:

Use GoDaddy to purchase your domain (e.g., tariqaziz.site).
Add Subdomain in Route 53:

Create a hosted zone for tariqaziz.site in Route 53.
Add a CNAME record for the subdomain resume.tariqaziz.site pointing to your CloudFront distribution.
Step 4: Secure the Website with SSL
Generate a Wildcard Certificate in ACM:

Request a certificate for *.tariqaziz.site.
Validate it using DNS validation (add the required CNAME in Route 53).
Attach the Certificate:

Attach the validated ACM certificate to your CloudFront distribution.
Step 5: Add Visitor Counter
Create a DynamoDB Table:

Create a table (e.g., resumeCounterDB) with id as the partition key.
Write the Lambda Function:

The function retrieves the current count from DynamoDB, increments it by 1, and stores it back.
Example code:
python
Copy
Edit
import json
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('resumeCounterDB')

def lambda_handler(event, context):
    response = table.get_item(Key={'id': '0'})
    views = response.get('Item', {}).get('views', 0)
    views += 1

    table.put_item(Item={'id': '0', 'views': views})
    return {'statusCode': 200, 'body': json.dumps({'views': views})}
Deploy Lambda:

Use the AWS Lambda console to upload the function.
Add an AWS Lambda Function URL for HTTP access.
Step 6: Integrate Visitor Counter in the Website
Update main.js:

Fetch visitor count from the Lambda Function URL:
javascript
Copy
Edit
const lambdaUrl = 'https://<your-lambda-url>';
fetch(lambdaUrl)
    .then(response => response.json())
    .then(data => {
        document.getElementById("counter").innerText = data.views;
    })
    .catch(error => console.error('Error:', error));
Embed the Counter in index.html:

Add the visitor count in your resume:
html
Copy
Edit
<p>This page has been viewed <span id="counter"></span> times.</p>
Technologies Used
S3: Static website hosting.
CloudFront: Global CDN for low latency.
Route 53: DNS management and subdomain configuration.
ACM: SSL/TLS certificate for secure HTTPS connections.
DynamoDB: Serverless NoSQL database for visitor count.
Lambda: Backend logic for incrementing visitor counter.
Acknowledgments
A huge thank you to Gwyneth Peña-Siguenza for the inspiring Azure Resume Challenge and to A Cloud Guru for their amazing resources that guided me through this project. This was a fantastic opportunity to showcase my AWS skills and learn new concepts along the way.

Project Links
Live Website: https://resume.tariqaziz.site
GitHub Repository: [Add Your GitHub Link Here]
Feel free to clone this repository, explore the code, and try building your own resume challenge with AWS or Azure! 😊 Let me know if you have any questions or need help.


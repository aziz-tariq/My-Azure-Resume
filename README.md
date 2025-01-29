
---

# **AWS Resume Challenge - Static Website Hosting with Visitor Counter**

This project implements the **Azure Resume Challenge** using **AWS services** to host a static resume website. It demonstrates how to build a secure, scalable, and globally accessible website with a visitor counter functionality.

---

## **Architecture Diagram**

```plaintext
┌────────────┐             ┌────────────┐
│  User      │             │  GoDaddy   │
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
│ S3 Bucket     │
│(Static Files) │
└─────┬─────────┘
      │
      ▼
┌───────────────┐
│ Lambda        │
│(Visitor Logic)│
└─────┬─────────┘
      │
      ▼
┌───────────────┐
│ DynamoDB      │
│(Visitor Count)│
└───────────────┘
```

---

## **Features**

- 🌐 **Static Website Hosting**: Hosted the resume on **S3** and optimized it with **CloudFront** for global low-latency delivery.
- 🕶️ **Custom Subdomain**: Configured the subdomain `resume.tariqaziz.site` via **GoDaddy** for DNS management.
- 🔒 **SSL Certificate**: Secured traffic with a **wildcard SSL certificate** issued via **AWS ACM**.
- 📊 **Visitor Counter**: Added real-time tracking of page visits using **AWS Lambda** and **DynamoDB**.

---

## **Step-by-Step Implementation**

### **Step 1: Create the S3 Bucket**
1. **Setup Static Website Hosting**:
   - Created an S3 bucket (e.g., `resume-static-site`) and enabled static website hosting.
   - Uploaded the `index.html` file and other static assets.

2. **Set Bucket Permissions**:
   - Configured the bucket policy to allow public access to objects served through CloudFront.

---

### **Step 2: Configure CloudFront**
1. **Create a CloudFront Distribution**:
   - Set the S3 bucket as the origin.
   - Enabled **ACM SSL/TLS Certificate** to serve the site over HTTPS.
   - Configured `index.html` as the **Default Root Object**.

2. **Cache Management**:
   - Enabled caching for better performance.
   - Used cache invalidation to reflect updates.

---

### **Step 3: Register Domain with GoDaddy**
1. **Purchase a Domain**:
   - Registered `tariqaziz.site` via **GoDaddy**.

2. **Set up a Subdomain**:
   - Configured a subdomain for `tariqaziz.site` so that I can use the domain for other projects.
   - Added a `CNAME` record for `resume.tariqaziz.site` pointing to the CloudFront distribution.

---

### **Step 4: Secure the Website with SSL**
1. **Generate Wildcard SSL Certificate**:
   - Used **AWS Certificate Manager (ACM)** to request a wildcard certificate for `*.tariqaziz.site`.
   - Validated the certificate using DNS validation through Route 53.

2. **Attach SSL to CloudFront**:
   - Attached the ACM certificate to the CloudFront distribution to enable HTTPS.

---

### **Step 5: Add Visitor Counter**
1. **Create DynamoDB Table**:
   - Created a table (`resumeCounterDB`) with `id` as the partition key.

2. **Write the Lambda Function**:
   - Lambda code to update and fetch visitor count:
     ```python
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
     ```

3. **Deploy Lambda Function**:
   - Deployed the Lambda function and configured a **Lambda Function URL** to allow HTTP requests.

---

### **Step 6: Integrate Visitor Counter with Frontend**
1. **Update `main.js`**:
   - Fetch visitor count from Lambda Function URL:
     ```javascript
     const lambdaUrl = 'https://<your-lambda-url>';
     fetch(lambdaUrl)
         .then(response => response.json())
         .then(data => {
             document.getElementById("counter").innerText = data.views;
         })
         .catch(error => console.error('Error:', error));
     ```

2. **Update `index.html`**:
   - Added a visitor count placeholder:
     ```html
     <p>This page has been viewed <span id="counter"></span> times.</p>
     ```

---

## **Technologies Used**

- **S3**: Static website hosting.
- **CloudFront**: Content delivery network for low latency.
- **GoDaddy**: DNS management for custom subdomain.
- **ACM**: SSL/TLS certificates for secure connections.
- **DynamoDB**: Serverless NoSQL database for visitor count tracking.
- **Lambda**: Serverless compute service for visitor counter logic.

---

## **Acknowledgments**

A huge thank you to **Gwyneth Peña-Siguenza** for the inspiring **Azure Resume Challenge** and to **A Cloud Guru** for providing the resources that guided me throughout this project.

---

## **Live Project**

- **Website**: [https://resume.tariqaziz.site](https://resume.tariqaziz.site)
- **GitHub Repository**: https://github.com/aziz-tariq/My-Azure-Resume

---

Feel free to clone this repository, explore the code, and implement your own resume project using AWS or Azure. Let me know if you have any questions or feedback! 😊

--- 

# 🚀 Secure S3 + CloudFront CI/CD with AWS CodePipeline

A secure and automated static website deployment project using **GitHub, AWS CodePipeline, Amazon S3, Amazon CloudFront, Origin Access Control (OAC), AWS WAF, AWS Shield Standard, Amazon CloudWatch, and Amazon SNS**.

The project demonstrates how to deploy a static website from a GitHub repository to a private S3 bucket using AWS CodePipeline, deliver the website globally through CloudFront, protect the application with WAF and Shield Standard, and monitor the infrastructure using CloudWatch and SNS.

---

## 🏗️ Architecture

```text
                         👤 USER
                            │
                            │ HTTPS
                            ▼
                    ┌─────────────────┐
                    │   CloudFront    │
                    │   CDN + HTTPS   │
                    └───────┬─────────┘
                            │
             ┌──────────────┼──────────────┐
             │                             │
             ▼                             ▼
      AWS Shield Standard              AWS WAF
       DDoS Protection              Web ACL / Rules
             │                             │
             └──────────────┬──────────────┘
                            │
                            ▼
                    ┌─────────────────┐
                    │       OAC       │
                    │ Origin Access   │
                    │    Control      │
                    └───────┬─────────┘
                            │
                            ▼
                    ┌─────────────────┐
                    │    Amazon S3    │
                    │  Private Bucket │
                    │ Static Website   │
                    └─────────────────┘


                    CI/CD PIPELINE
                         │
                         ▼
                    ┌──────────┐
                    │  GitHub  │
                    │ Repository│
                    └────┬─────┘
                         │
                         ▼
                  AWS CodePipeline
                         │
                         ▼
                    Amazon S3
                         │
                         ▼
                  CloudFront CDN
                         │
                         ▼
                    Live Website


                    MONITORING
                         │
             ┌───────────┴───────────┐
             ▼                       ▼
        CloudWatch                  AWS WAF
         Metrics                   Metrics
             │                       │
             └───────────┬───────────┘
                         ▼
                    CloudWatch
                       Alarms
                         │
                         ▼
                        SNS
                         │
                         ▼
                   📧 Email Alerts
```

---

# 🎯 Project Objectives

This project was built to demonstrate:

- Secure static website hosting using Amazon S3
- Private S3 bucket architecture
- Amazon CloudFront CDN
- Origin Access Control (OAC)
- HTTPS content delivery
- GitHub integration
- AWS CodePipeline CI/CD
- Automated deployment to S3
- CloudFront content delivery
- AWS WAF protection
- AWS Shield Standard DDoS protection
- CloudWatch monitoring
- CloudWatch alarms
- SNS email notifications
- Basic production-style AWS architecture

---

# 🛠️ AWS Services Used

| Service | Purpose |
|---|---|
| **GitHub** | Source code repository |
| **AWS CodePipeline** | CI/CD automation |
| **Amazon S3** | Website file storage |
| **Amazon CloudFront** | CDN and HTTPS delivery |
| **Origin Access Control (OAC)** | Secure CloudFront → S3 access |
| **AWS WAF** | Web application protection |
| **AWS Shield Standard** | DDoS protection |
| **Amazon CloudWatch** | Monitoring and alarms |
| **Amazon SNS** | Email notifications |
| **AWS IAM** | Access control and permissions |

---

# 🔄 CI/CD Architecture

The project uses **AWS CodePipeline** instead of GitHub Actions.

```text
Developer
    │
    ▼
GitHub Repository
    │
    │ Code changes / Push
    ▼
AWS CodePipeline
    │
    ▼
Deployment Stage
    │
    ▼
Amazon S3
    │
    ▼
CloudFront
    │
    ▼
Live Website
```

### CI/CD Flow

1. Developer pushes code to GitHub.
2. GitHub is connected as the source provider for AWS CodePipeline.
3. CodePipeline detects the source change.
4. CodePipeline retrieves the latest source code.
5. The deployment stage uploads the website files to S3.
6. CloudFront serves the updated website.
7. If CloudFront invalidation is configured, cached content is invalidated after deployment.

---

# 📦 GitHub Repository

GitHub is used as the source code repository.

Example repository structure:

```text
s3-cloudfront-cicd/
│
├── index.html
├── style.css
├── script.js
└── assets/
    ├── images/
    └── icons/
```

The repository contains the static website files that are deployed through CodePipeline.

---

# ⚙️ AWS CodePipeline

AWS CodePipeline automates the deployment process.

### Pipeline Flow

```text
GitHub
   │
   ▼
Source Stage
   │
   ▼
Deploy Stage
   │
   ▼
Amazon S3
```

The pipeline is connected directly to the GitHub repository.

When a new change is pushed to the configured branch, CodePipeline can automatically start a new pipeline execution.

---

# 🗄️ Amazon S3

Amazon S3 is used to store the static website files.

The bucket is configured as a **private bucket**.

Example:

```text
Amazon S3
│
├── index.html
├── style.css
├── script.js
└── assets/
```

### S3 Security

Public access is blocked.

Users do not directly access the S3 bucket.

Instead:

```text
User
  │
  ▼
CloudFront
  │
  │ OAC
  ▼
Private S3
```

This provides a more secure architecture than exposing the S3 bucket publicly.

---

# 🔐 Origin Access Control (OAC)

**Origin Access Control** is used to allow CloudFront to securely access objects stored in the private S3 bucket.

```text
Internet
    │
    ▼
CloudFront
    │
    │ OAC
    ▼
Private S3
```

OAC helps prevent users from bypassing CloudFront and accessing the S3 origin directly.

The S3 bucket policy grants the CloudFront distribution permission to retrieve objects.

---

# 🌎 Amazon CloudFront

Amazon CloudFront is used as the CDN layer.

It provides:

- Global content delivery
- Edge caching
- Reduced latency
- HTTPS
- Integration with AWS WAF
- Integration with AWS Shield
- Secure access to the private S3 origin

Request flow:

```text
User
  ↓
CloudFront Edge Location
  ↓
OAC
  ↓
Private S3
```

---

# 🔐 HTTPS

CloudFront provides HTTPS delivery for the website.

The website can be accessed using the CloudFront distribution domain:

```text
https://YOUR-DISTRIBUTION.cloudfront.net
```

HTTP can be configured to redirect to HTTPS.

For a custom domain, **Amazon Route 53** and **AWS Certificate Manager (ACM)** can be added.

---

# 🛡️ AWS WAF

AWS WAF is used to protect the CloudFront distribution from unwanted or malicious web requests.

A **Web ACL** is associated with the CloudFront distribution.

```text
User Request
      │
      ▼
  CloudFront
      │
      ▼
    AWS WAF
      │
   ┌──┴──┐
   │     │
 Allow  Block
   │     │
   ▼     X
  OAC
   │
   ▼
 S3
```

### WAF protections can include:

- AWS Managed Rules
- Common Rule Set
- Known Bad Inputs
- Rate-based rules
- IP-based rules
- Request filtering

---

# 🛡️ AWS Shield Standard

AWS Shield Standard provides automatic baseline DDoS protection for supported AWS services, including CloudFront.

Shield Standard does not require a separate manual attachment to CloudFront.

```text
Internet
    │
    ▼
CloudFront
    │
    ▼
Shield Standard
    │
    ▼
AWS WAF
    │
    ▼
Private S3
```

For this project, Shield Standard provides the baseline DDoS protection required for the architecture.

---

# 📊 CloudWatch Monitoring

Amazon CloudWatch is used to monitor CloudFront and WAF activity.

### CloudFront Metrics

Important metrics include:

```text
Requests
4xxErrorRate
5xxErrorRate
BytesDownloaded
```

### WAF Metrics

Important metrics include:

```text
AllowedRequests
BlockedRequests
CountedRequests
```

These metrics can be used to understand website traffic and detect abnormal activity.

---

# 🚨 CloudWatch Alarms

CloudWatch alarms are configured to monitor important conditions.

## 1. CloudFront 5xx Error Alarm

Example configuration:

```text
Metric:
5xxErrorRate

Condition:
Greater than 5%

Action:
SNS notification
```

Flow:

```text
5xxErrorRate > 5%
        │
        ▼
CloudWatch Alarm
        │
        ▼
       SNS
        │
        ▼
   📧 Email Alert
```

This helps identify potential origin or service-side problems.

---

## 2. CloudFront 4xx Error Alarm

Example configuration:

```text
Metric:
4xxErrorRate

Condition:
Greater than 10%

Action:
SNS notification
```

This can help identify an unusual increase in client-side or invalid requests.

Thresholds should be adjusted according to normal application traffic.

---

## 3. WAF Blocked Requests Alarm

CloudWatch can also monitor:

```text
BlockedRequests
```

Example:

```text
WAF Blocked Requests
        │
        ▼
CloudWatch
        │
        ▼
Alarm
        │
        ▼
SNS
        │
        ▼
Email Alert
```

An unusual increase in blocked requests can indicate suspicious traffic or attack attempts.

---

# 📧 Amazon SNS

Amazon SNS is used to send CloudWatch alarm notifications.

Architecture:

```text
CloudWatch
     │
     ▼
CloudWatch Alarm
     │
     ▼
SNS Topic
     │
     ▼
Email Subscription
     │
     ▼
📧 Email
```

The email subscription must be confirmed before notifications can be received.

The same SNS topic can be used by multiple CloudWatch alarms.

---

# 🔐 IAM

AWS IAM controls access between the services.

IAM is used for:

- CodePipeline permissions
- S3 deployment permissions
- CloudFront permissions
- WAF access
- CloudWatch access
- SNS notifications

The principle of **least privilege** should be followed.

Only the permissions required by the pipeline and AWS services should be granted.

---

# 🚀 Deployment Process

## Step 1 — Create GitHub Repository

Create a GitHub repository containing the static website files.

Example:

```text
index.html
style.css
script.js
assets/
```

---

## Step 2 — Create S3 Bucket

Create an S3 bucket for website storage.

Recommended configuration:

```text
Block Public Access:
Enabled

Bucket:
Private
```

The bucket should not be publicly readable.

---

## Step 3 — Create CloudFront Distribution

Create a CloudFront distribution with the S3 bucket as the origin.

Configure:

```text
Origin:
S3 Bucket

Origin Access:
Origin Access Control (OAC)

Viewer Protocol Policy:
Redirect HTTP to HTTPS
```

---

## Step 4 — Configure OAC

Create an Origin Access Control.

Attach the OAC to the CloudFront S3 origin.

Update the S3 bucket policy to allow the CloudFront distribution to access the objects.

---

## Step 5 — Configure Default Root Object

Set:

```text
index.html
```

as the CloudFront default root object.

---

## Step 6 — Configure AWS WAF

Create a Web ACL for CloudFront.

Add required managed and custom rules.

Associate the Web ACL with the CloudFront distribution.

---

## Step 7 — Configure CodePipeline

Create an AWS CodePipeline pipeline.

### Source Stage

Connect:

```text
GitHub Repository
```

Select the required:

```text
Repository
Branch
```

### Deploy Stage

Configure the deployment to:

```text
Amazon S3
```

Select the website S3 bucket.

The pipeline will then deploy the GitHub source files to the S3 bucket.

---

# 🔄 CodePipeline Deployment Flow

```text
GitHub
   │
   │ Push
   ▼
CodePipeline
   │
   ▼
Source Stage
   │
   ▼
Deploy Stage
   │
   ▼
S3 Bucket
   │
   ▼
CloudFront
   │
   ▼
Live Website
```

---

# 🔄 CloudFront Cache Invalidation

CloudFront caches objects at edge locations.

After updating files in S3, CloudFront may continue serving cached versions until the cache expires.

If cache invalidation is configured in the deployment process, the cache can be invalidated after deployment.

Example:

```text
S3 Updated
    │
    ▼
CloudFront Invalidation
    │
    ▼
New Content Served
```

Example CLI command:

```bash
aws cloudfront create-invalidation \
  --distribution-id DISTRIBUTION-ID \
  --paths "/*"
```

For production environments, invalidating only changed files can reduce unnecessary invalidation requests.

---

# 🧪 Testing

## Website Test

Open:

```text
https://YOUR-DISTRIBUTION.cloudfront.net
```

Verify that the website loads correctly.

---

## HTTPS Test

Verify that the website uses HTTPS.

If HTTP-to-HTTPS redirection is enabled:

```text
http://your-site
        ↓
https://your-site
```

---

## CI/CD Test

Make a change to the website.

For example:

```text
index.html
```

Commit and push the change:

```bash
git add .
git commit -m "update website"
git push
```

Then check:

```text
GitHub
   ↓
CodePipeline
   ↓
Source Stage
   ↓
Deploy Stage
   ↓
S3
   ↓
CloudFront
```

Verify that the updated website is available.

---

## Monitoring Test

Open:

```text
CloudWatch
→ Alarms
```

Check the configured alarms.

Possible states:

```text
OK
INSUFFICIENT_DATA
ALARM
```

### OK

The metric is within the configured threshold.

### INSUFFICIENT_DATA

CloudWatch does not currently have enough metric data to evaluate the alarm.

### ALARM

The configured threshold has been breached.

---

# 📁 Project Structure

```text
s3-cloudfront-cicd/
│
├── index.html
├── style.css
├── script.js
│
└── assets/
    ├── images/
    └── icons/
```

The project does not require a GitHub Actions workflow because **AWS CodePipeline handles the CI/CD process**.

---

# 💰 Cost Considerations

The main AWS services that may generate charges include:

- Amazon S3
- CloudFront
- AWS WAF
- CloudWatch
- SNS
- CloudFront invalidations

Costs depend on traffic, requests, storage, monitoring configuration, and other usage.

For cost optimization:

- Keep unnecessary S3 objects removed.
- Use appropriate CloudFront caching.
- Avoid unnecessary cache invalidations.
- Configure CloudWatch alarms only where useful.
- Use appropriate WAF rules.
- Monitor AWS billing and usage.

---

# 🔒 Security Best Practices

- Keep the S3 bucket private.
- Enable S3 Block Public Access.
- Use CloudFront OAC.
- Do not expose the S3 origin publicly.
- Use HTTPS.
- Attach AWS WAF to CloudFront.
- Use AWS managed WAF rules where appropriate.
- Configure rate-based rules where required.
- Use Shield Standard for baseline DDoS protection.
- Follow IAM least privilege.
- Avoid hardcoded AWS credentials.
- Protect the GitHub repository.
- Monitor CloudFront and WAF metrics.
- Configure SNS alerts for important conditions.

---

# 📈 Complete Request Flow

```text
                         INTERNET
                            │
                            ▼
                    ┌───────────────┐
                    │   CloudFront  │
                    │   HTTPS / CDN │
                    └───────┬───────┘
                            │
                ┌───────────┴───────────┐
                │                       │
                ▼                       ▼
        Shield Standard              AWS WAF
        DDoS Protection           Web ACL Rules
                                        │
                                  ┌─────┴─────┐
                                  │           │
                               BLOCK        ALLOW
                                  │           │
                                  X           ▼
                                          OAC
                                           │
                                           ▼
                                      Private S3
                                           │
                                           ▼
                                        Website
```

---

# 📊 Monitoring Flow

```text
CloudFront ───────────────┐
                           │
                           ▼
                       CloudWatch
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
             4xx          5xx      WAF Blocks
              │            │            │
              └────────────┼────────────┘
                           ▼
                     CloudWatch Alarm
                           │
                           ▼
                          SNS
                           │
                           ▼
                     📧 Email Alert
```

---

# 🔄 Complete CI/CD + Security Flow

```text
                         👨‍💻 DEVELOPER
                              │
                              ▼
                        GitHub Repository
                              │
                              ▼
                       AWS CodePipeline
                              │
                              ▼
                         Amazon S3
                              │
                              │
                              ▼
                       CloudFront CDN
                              │
                  ┌───────────┴───────────┐
                  │                       │
                  ▼                       ▼
            Shield Standard             AWS WAF
            DDoS Protection            Web ACL
                  │                       │
                  └───────────┬───────────┘
                              ▼
                             OAC
                              │
                              ▼
                         Private S3
                              │
                              ▼
                         🌐 Website


                    MONITORING
                         │
                         ▼
                     CloudWatch
                         │
                         ▼
                       Alarms
                         │
                         ▼
                        SNS
                         │
                         ▼
                    📧 Email Alert
```

---

# 🎓 What I Learned

Through this project, I learned and practiced:

- Amazon S3
- Private S3 bucket configuration
- CloudFront CDN
- Origin Access Control
- HTTPS
- AWS WAF
- AWS Shield Standard
- CloudWatch monitoring
- CloudWatch alarms
- Amazon SNS
- IAM permissions
- GitHub
- AWS CodePipeline
- CI/CD automation
- CloudFront cache invalidation
- Static website deployment
- Cloud security
- Monitoring and alerting
- Production-style AWS architecture

---

# 🔮 Future Improvements

Possible future enhancements:

- Custom domain with Route 53
- AWS Certificate Manager custom SSL certificate
- Advanced WAF rules
- WAF logging
- CloudFront access logging
- CloudWatch dashboard
- Automated deployment rollback
- Blue/green deployment
- Infrastructure as Code with Terraform
- Infrastructure as Code with CloudFormation
- Automated testing in the pipeline
- Separate development and production environments
- Additional monitoring and alerting

---

# 📌 Project Summary

This project implements a secure and automated static website deployment architecture using AWS:

```text
GitHub
   ↓
AWS CodePipeline
   ↓
Amazon S3
   ↓
CloudFront
   ↓
OAC
   ↓
Private S3

Security:
AWS WAF + Shield Standard

Monitoring:
CloudWatch + SNS

Delivery:
HTTPS + CDN
```

The project demonstrates a practical **AWS Cloud Engineer / DevOps-style architecture** combining CI/CD, secure object storage, CDN delivery, web application protection, DDoS protection, monitoring, and automated alerting.

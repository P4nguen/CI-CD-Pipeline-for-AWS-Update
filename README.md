# 🚀 Automated CI/CD Pipeline: GitHub Actions to AWS S3

## 📌 Project Overview
This repository demonstrates a fully automated Continuous Integration and Continuous Deployment (CI/CD) pipeline. It bridges the gap between version control and cloud hosting by automatically deploying a static website to an **AWS S3 Bucket** whenever new code is pushed to the `main` branch. 

The goal of this project is to showcase modern **DevOps practices**, serverless architecture, and infrastructure security without relying on manual server provisioning.

## 🏗️ Architecture & Workflow
The pipeline operates seamlessly without human intervention:
1. **Developer Action:** Code (`index.html`) is updated and pushed to the GitHub repository.
2. **Trigger:** GitHub Actions detects the push event on the `main` branch.
3. **Authentication:** The workflow securely authenticates with AWS using encrypted GitHub Secrets (IAM User credentials).
4. **Deployment:** The AWS CLI syncs the updated repository files directly to the S3 bucket.
5. **Live Status:** The website reflects the changes globally in seconds.

## 🛠️ Technologies Used
* **AWS S3:** Configured for Serverless Static Website Hosting.
* **AWS IAM:** Access management using the principle of least privilege.
* **GitHub Actions:** Workflow automation and CI/CD runner execution (`deploy.yml`).
* **Git:** Version control.
* **HTML5:** Frontend content.

## ☁️ AWS Infrastructure Configurations (Under the Hood)
While the deployment is fully automated via GitHub, the following cloud infrastructure configurations were implemented on the AWS Console to securely support this architecture:
* **S3 Static Web Hosting:** Enabled to serve the bucket contents as a public-facing website.
* **Bucket Policy (JSON):** Crafted a custom policy granting `s3:GetObject` permissions to allow public read access while protecting against unauthorized writes.
* **IAM Security:** Created a dedicated IAM user specifically for GitHub Actions with strict programmatic access keys, ensuring the root account remains completely isolated and secure.

## 💡 Key Takeaways
This project highlights the transition from traditional EC2 server management to a highly scalable, cost-effective **Serverless** approach, emphasizing the power of "Infrastructure as Code" concepts and deployment automation.

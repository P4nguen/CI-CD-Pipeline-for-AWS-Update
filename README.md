# 🚀 Automated CI/CD Pipeline: GitHub Actions to AWS S3

## 📌 Project Overview
This repository demonstrates a fully automated Continuous Integration and Continuous Deployment (CI/CD) pipeline. It bridges the gap between version control and cloud hosting by automatically deploying a static website to an **AWS S3 Bucket** whenever new code is pushed to the `main` branch.

The goal of this project is to showcase modern **DevOps practices**, serverless architecture, and infrastructure security without relying on manual server provisioning.

---

## 🏗️ Architecture & Workflow
The pipeline operates seamlessly without human intervention:
1. **Developer Action:** Code (`index.html`) is updated and pushed to the GitHub repository.
2. **Trigger:** GitHub Actions detects the push event on the `main` branch.
3. **Authentication:** The workflow securely authenticates with AWS using encrypted GitHub Secrets (IAM User credentials).
4. **Deployment:** The AWS CLI syncs the updated repository files directly to the S3 bucket.
5. **Live Status:** The website reflects the changes globally in seconds.

---

## 🛠️ Technologies Used
- **AWS S3:** Configured for Serverless Static Website Hosting.
- **AWS IAM:** Access management using the principle of least privilege.
- **GitHub Actions:** Workflow automation and CI/CD runner execution (`deploy.yml`).
- **Git:** Version control.
- **HTML5/CSS:** Frontend content.

---

## 🚀 How to Replicate This CI/CD Pipeline (Step-by-Step Guide)

### Phase 1: Create and Configure the AWS S3 Bucket
1. Log in to the **AWS Management Console** and navigate to the **S3** service.
2. Click **Create bucket**.
3. Choose a globally unique name (e.g., `my-cicd-website-bucket`) and select your preferred region.
4. Scroll down to **Block Public Access settings for this bucket** and **uncheck** "Block all public access". Acknowledge the warning.
5. Click **Create bucket**.
6. Open your newly created bucket, go to the **Properties** tab, and scroll to the bottom.
7. Click **Edit** under **Static website hosting**.
8. Select **Enable**, specify `index.html` as the Index document, and click **Save changes**.

---

### Phase 2: Apply the S3 Bucket Policy
To make the website visible to the internet, you must attach a public read policy.
1. Inside your bucket, go to the **Permissions** tab.
2. Scroll down to **Bucket policy** and click **Edit**.
3. Paste the following JSON policy. **Important:** Replace `YOUR-BUCKET-NAME` with your actual bucket name!
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

4. Click **Save changes**. Your bucket should now have a red **"Public"** tag.

---

### Phase 3: Create an AWS IAM User for GitHub Actions
> **Security Note:** Never use the root account or grant Admin privileges to a CI/CD bot. Grant access only to S3.

1. Navigate to the **IAM** service in the AWS Console.
2. Go to **Users** and click **Create user**. Name it something like `github-actions-bot` and click **Next**.
3. Select **Attach policies directly**.
4. Search for `AmazonS3FullAccess`, check the box next to it, and create the user.
5. Open the newly created user, go to the **Security credentials** tab.
6. Scroll down to **Access keys** and click **Create access key**.
7. Choose **Command Line Interface (CLI)**, acknowledge the warning, and click **Next** to create.
8. ⚠️ **CRITICAL:** Copy the **Access key ID** and **Secret access key**. You will **not** be able to see the secret key again.

---

### Phase 4: Configure GitHub Secrets
1. Fork or clone this repository to your own GitHub account.
2. Go to your repository's **Settings** tab.
3. On the left sidebar, expand **Secrets and variables** and click **Actions**.
4. Click **New repository secret**.
5. Add the following two secrets using the keys you generated in AWS IAM:

| Name | Value |
|------|-------|
| `AWS_ACCESS_KEY_ID` | Paste your Access Key ID |
| `AWS_SECRET_ACCESS_KEY` | Paste your Secret Access Key |

---

### Phase 5: Update the Workflow File
1. In your repository, navigate to `.github/workflows/deploy.yml` (create these folders if they don't exist).
2. Edit the file to match your specific AWS setup:
   - Update `aws-region` to match where you created your bucket (e.g., `eu-central-1`).
   - Update the bucket name in the `aws s3 sync` command at the bottom of the file:
```bash
aws s3 sync . s3://YOUR-BUCKET-NAME --exclude ".git/*" --exclude ".github/*"
```

3. Save and commit the changes.

---

### Phase 6: Trigger the Magic! ✨
1. Pull the repository to your local machine.
2. Make a change to the `index.html` file (e.g., update the `<h1>` tag).
3. Commit and push the changes to the `main` branch:
```bash
git add .
git commit -m "Testing CI/CD pipeline"
git push origin main
```

4. Go to the **Actions** tab in your GitHub repository to watch the deployment run automatically.
5. Once it turns ✅ green, visit your S3 Bucket's **website endpoint URL** to see your live updates!

# End-to-End CI/CD Pipeline for Python Web App using AWS Elastic Beanstalk, CodeBuild, CodePipeline

---

## 1. **GitHub Repository Setup (5 Minutes)**

### Steps:

1. **Create a GitHub Repository**

   * Repository Name: `simple-web-app`
   * Initialize with README.md

2. **Write and Push Application Code**

   * Application: Simple Python Flask App

### Folder Structure:

```
simple-web-app/
├── application.py
├── requirements.txt
├── .ebextensions/
│   └── python.config
├── buildspec.yml
└── Procfile
```

### File Explanations:

* **application.py**: Main Flask application script.
* **requirements.txt**: Lists Python dependencies for CodeBuild & Elastic Beanstalk.
* **.ebextensions/python.config**: Configuration for Elastic Beanstalk environment (e.g., Python version).
* **buildspec.yml**: Instructions for CodeBuild on how to build the application.
* **Procfile** is essential for Elastic Beanstalk to correctly identify and start your Flask application.

---

## 2. **Elastic Beanstalk Environment Setup (10 Minutes)**

### Steps:

1. Navigate to **Elastic Beanstalk Console**.
2. Click **Create Application**.

   * Name: `simple-web-app`
   * Platform: Python
   * Environment Type: Web Server
3. Upload Sample Code (or leave it blank if using CodePipeline deployment).
4. **Create Environment**.

### IAM Roles Required:

* **ElasticBeanstalkServiceRole**

  * AWS managed policy: `AWSElasticBeanstalkManagedUpdatesCustomerRolePolicy`
* **ElasticBeanstalkEC2InstanceProfile**

  * Policies:

    * `AWSElasticBeanstalkWebTier`
    * `AWSElasticBeanstalkWorkerTier`
    * `AmazonS3ReadOnlyAccess`

---

## 3. **CodeBuild Project Setup (5 Minutes)**

### Steps:

1. Navigate to **AWS CodeBuild Console** → Create Build Project
2. Project Name: `simple-web-app-build`
3. Source Provider: GitHub
4. Environment:

   * Managed Image: Ubuntu / Standard 5.0
   * Runtime: Python 3.x
5. Service Role:

   * Create a new Role or Use Existing: `CodeBuildServiceRole-simple-web-app`
6. Buildspec file:

   * Use buildspec.yml in source repo.
7. Artifacts:

   * Type: Amazon S3
   * Bucket: (Create S3 Bucket for artifacts)

### IAM Role for CodeBuild:

* **CodeBuildServiceRole-simple-web-app**

  * Policies:

    * `AmazonS3FullAccess` (for artifacts)
    * `AWSCodeBuildDeveloperAccess`

---

## 4. **CodePipeline Setup (10 Minutes)**

### Steps:

1. Navigate to **AWS CodePipeline Console** → Create Pipeline
2. Pipeline Name: `simple-web-app-pipeline`
3. Service Role:

   * Create or Use Existing: `AWSCodePipelineServiceRole-ap-south-1-simple-web-app-pipeline`
4. Stages:

#### Stage 1: Source

* Provider: GitHub
* Repository: `simple-web-app`

#### Stage 2: Build

* Provider: CodeBuild
* Project: `simple-web-app-build`

#### Stage 3: Deploy

* Provider: Elastic Beanstalk
* Application Name: `simple-web-app`
* Environment Name: (Elastic Beanstalk Environment)

### IAM Role for CodePipeline:

* **AWSCodePipelineServiceRole-ap-south-1-simple-web-app-pipeline**

  * Custom Inline Policy:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "codebuild:StartBuild",
                "codebuild:BatchGetBuilds",
                "elasticbeanstalk:CreateApplicationVersion",
                "elasticbeanstalk:UpdateEnvironment",
                "elasticbeanstalk:DescribeApplications",
                "elasticbeanstalk:DescribeEnvironments",
                "s3:GetObject",
                "s3:GetObjectVersion"
            ],
            "Resource": "*"
        }
    ]
}
```

---

## 5. **Finalize Pipeline and Test (5 Minutes)**

### Steps:

1. Go to **CodePipeline Console** → simple-web-app-pipeline
2. Click **Release Change**
3. Monitor Source → Build → Deploy stages.
4. Validate deployment on Elastic Beanstalk URL.

---

## Workflow Diagram (GitHub → CodePipeline → CodeBuild → Elastic Beanstalk)

```
[GitHub Repo]
     |
     v
[CodePipeline (Source Stage)]
     |
     v
[CodeBuild (Build Stage)]
     |
     v
[Elastic Beanstalk (Deploy Stage)]
     |
     v
[Live Application URL]
```

---

## IAM Roles Recap:

| Role Name                                                     | Policies Attached                                                                 |
| ------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| ElasticBeanstalkServiceRole                                   | AWSElasticBeanstalkManagedUpdatesCustomerRolePolicy                               |
| ElasticBeanstalkEC2InstanceProfile                            | AWSElasticBeanstalkWebTier, AWSElasticBeanstalkWorkerTier, AmazonS3ReadOnlyAccess |
| CodeBuildServiceRole-simple-web-app                           | AWSCodeBuildDeveloperAccess, AmazonS3FullAccess                                   |
| AWSCodePipelineServiceRole-ap-south-1-simple-web-app-pipeline | Custom Inline Policy (CodeBuild, Elastic Beanstalk, S3 access)                    |

---

## Summary:

* **GitHub**: Source Code Repository.
* **CodeBuild**: Build artifacts and application packages.
* **Elastic Beanstalk**: Deployment Platform (PaaS).
* **CodePipeline**: Automates end-to-end CI/CD flow.
* **IAM Roles**: Secure permission boundaries for each service.

---

## Optional Enhancements:

* Add **Approval Stage** before Deploy in CodePipeline.
* Use **Elastic Beanstalk Docker Platform** with Dockerrun.aws.json.
* Configure **Notifications** via SNS for pipeline events.
* Add **CodeDeploy** if you're using EC2 or ECS instead of Beanstalk.

---

This completes the full automated CI/CD pipeline setup for deploying a Python web application using GitHub, AWS CodeBuild, CodePipeline, and Elastic Beanstalk.
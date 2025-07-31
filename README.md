# README.md

# Simple Web App

This project is a simple Flask web application that is designed to be deployed on AWS Elastic Beanstalk. It serves a greeting message when accessed.

## Project Structure

```
simple-web-app
├── app.py
├── requirements.txt
├── .ebextensions
│   └── python.config
├── buildspec.yml
├── .elasticbeanstalk
│   └── config.yml
├── README.md
```

## Setup Instructions

1. **Clone the Repository**: Clone this repository to your local machine.

2. **Install Dependencies**: Navigate to the project directory and install the required Python packages:
   ```
   pip install -r requirements.txt
   ```

3. **Run the Application Locally**: You can run the Flask application locally using:
   ```
   python app.py
   ```
   Access the application at `http://127.0.0.1:5000/`.

## Deployment to AWS Elastic Beanstalk

### Prerequisites

- Ensure you have the AWS CLI and Elastic Beanstalk CLI installed and configured with your AWS credentials.

### Steps to Deploy

1. **Initialize Elastic Beanstalk**: Run the following command in your project directory to initialize Elastic Beanstalk:
   ```
   eb init -p python-3.x your-application-name
   ```

2. **Create an Environment**: Create an Elastic Beanstalk environment:
   ```
   eb create your-environment-name
   ```

3. **Deploy the Application**: Deploy your application to Elastic Beanstalk:
   ```
   eb deploy
   ```

4. **Access Your Application**: Once deployed, you can access your application using the URL provided by Elastic Beanstalk.

## CodePipeline Setup

To automate the build and deployment process, you can set up AWS CodePipeline:

1. Go to the AWS Management Console and navigate to CodePipeline.
2. Create a new pipeline and connect it to your source repository (e.g., GitHub).
3. Add a build stage that uses CodeBuild with the `buildspec.yml` you created.
4. Add a deploy stage that deploys to your Elastic Beanstalk environment.

## Note

Make sure to replace `your-application-name` and `your-environment-name` with your actual application and environment names in the commands. Adjust the Python version in the configuration files as needed.
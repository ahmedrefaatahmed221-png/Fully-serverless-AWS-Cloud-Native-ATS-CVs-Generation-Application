# AWS Cloud-Native ATS CV Generator & Analyzer

![Architecture Diagram](images/ATSArchDesign.png)

## 📌 Project Overview
This project is an AWS cloud-native application designed to generate Applicant Tracking System (ATS) friendly CVs and analyze them against specific Job Descriptions (JD). 

The application utilizes a hybrid architecture, combining highly available EC2 instances for the web frontend (Flask) with powerful serverless computing (AWS Lambda, API Gateway, S3, DynamoDB) for the backend processing.

## 🏗️ Architecture & Workflow

The system is designed with high availability and security in mind, utilizing a custom VPC (`10.0.0.0/16`). Here is the step-by-step request flow:

1. **User Request:** The user interacts with the application via the internet.
2. **Application Load Balancer (ALB):** Receives the incoming traffic and distributes it evenly across the backend EC2 instances.
3. **Web Tier (Public Subnets):** 
   - Two `t2.micro` EC2 instances run a Python Flask application in separate availability zones.
   - **Security:** Protected by Security Groups allowing HTTP (Port 80) access *only* from the ALB.
4. **API Gateway (Private Zone):** The Flask application securely communicates with an API Gateway which acts as the entry point for backend services with two main endpoints:
   - `POST /generate`
   - `POST /analyze`
5. **Serverless Compute (AWS Lambda):**
   - **Lambda: CV Generator:** Triggered by `/generate`. It takes data from the frontend form, generates a text-based CV, and saves the file to an **Amazon S3 Bucket**.
   - **Lambda: JD Analyzer:** Triggered by `/analyze`. It compares the CV against the provided Job Description and stores the analysis data/results in **Amazon DynamoDB**.
6. **IAM Roles:** Securely manage permissions, ensuring Lambda functions have the exact privileges needed to read/write to S3 and DynamoDB.

## 🛠️ Tech Stack & AWS Services
* **Frontend/Web Server:** Python, Flask, HTML/CSS
* **Compute:** Amazon EC2 (`t2.micro`), AWS Lambda
* **Networking & Traffic:** Amazon VPC, Application Load Balancer (ALB), API Gateway
* **Storage & Database:** Amazon S3, Amazon DynamoDB
* **Security:** AWS IAM, Security Groups

## 📂 Repository Structure

```text
├── backend/                  # Flask application code (app.py, requirements.txt)
├── images/                   # Architecture diagrams and project screenshots
│   └── ATSArchDesign.png
├── lambda_analyzer/          # Source code for the JD Analyzer Lambda function
├── lambda_generator/         # Source code for the CV Generator Lambda function
├── templates/                # HTML templates for the Flask frontend
└── README.md                 # Project documentation

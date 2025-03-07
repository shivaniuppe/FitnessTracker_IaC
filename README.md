# Fitness Tracker Cloud Application

## Project Overview

This project provisions a **serverless fitness tracking application** using AWS Cloud services, orchestrated through **Infrastructure-as-Code (IaC)** with AWS CloudFormation. The application enables user management, fitness data submission, and goal tracking, with notifications triggered upon achieving fitness goals. The project adheres to the **AWS Well-Architected Framework**, ensuring **security, reliability, performance efficiency, cost optimization, and operational excellence**.

---

## Features

- **User Registration**: Users can register by providing their email, which is securely stored in a DynamoDB table.
- **Fitness Data Submission**: Users can submit fitness data (e.g., steps taken), stored in a separate DynamoDB table.
- **Fitness Goal Tracking**: The application tracks user fitness goals and sends **SNS notifications** when goals are achieved.
- **API Integration**: RESTful APIs exposed via **API Gateway** trigger AWS Lambda functions for data handling and processing.
- **Event-Driven Notifications**: A **Step Functions** state machine orchestrates notifications when fitness goals are achieved.
- **Security & Compliance**: Implements encryption, IAM roles, and **Cognito** for secure user authentication and authorization.
- **Fault Tolerance**: Uses **dead-letter queues (DLQ)** and retries for Lambda and EventBridge to ensure no data loss.
- **Monitoring & Logging**: Integrated with **CloudWatch** and **X-Ray** for observability and quick issue resolution.

---

## Architecture Diagram

The architecture includes:

- **AWS DynamoDB**: Stores user and fitness data with serverless billing, encryption, and point-in-time recovery.
- **AWS Lambda**: Processes user registration, fitness data submissions, and notifications.
- **Amazon SNS**: Sends notifications to users when fitness goals are achieved.
- **AWS Step Functions**: Orchestrates event-driven workflows for fitness goal achievements.
- **Amazon API Gateway**: Exposes RESTful APIs for client interaction, with **Cognito** authorizers and usage plans.
- **CloudWatch & X-Ray**: Provides monitoring, logging, and tracing for operational excellence.

---

## Components

### 1. **DynamoDB Tables**
- **Users Table**:
  - Stores user information (`userId`, `email`, `fitnessGoal`).
  - Configured with **serverless billing mode**, **encryption at rest**, and **point-in-time recovery**.
- **FitnessData Table**:
  - Stores fitness data (`userId`, `timestamp`, `steps`).
  - Configured with **streams** for real-time data processing.

### 2. **Lambda Functions**
- **CreateUserFunction**:
  - Registers users, subscribes them to SNS, and stores data in DynamoDB.
- **GetUserFunction**:
  - Retrieves user details from DynamoDB.
- **SubmitFitnessDataFunction**:
  - Submits fitness data, updates total steps, and triggers Step Functions for goal achievements.
- **GetFitnessDataFunction**:
  - Retrieves fitness data for a specific user.

### 3. **API Gateway**
- Exposes RESTful endpoints:
  - `POST /users`: Registers a new user.
  - `GET /users/{userId}/{email}`: Retrieves user details.
  - `POST /fitness`: Submits fitness data.
  - `GET /fitness/{userId}`: Retrieves fitness data.
- Configured with **Cognito authorizers**, **usage plans**, and **caching** for secure and efficient API access.

### 4. **Step Functions & SNS**
- **FitnessGoalStateMachine**:
  - Orchestrates notifications via SNS when fitness goals are achieved.
  - Implements **retries** and **dead-letter queues (DLQ)** for fault tolerance.

---

## Well-Architected Framework Principles

### 1. **Security**
- Enabled **encryption at rest** for DynamoDB tables and **in-transit encryption** for API Gateway.
- Integrated **Cognito** for secure user authentication and authorization.
- Used **IAM roles** and policies to enforce least privilege access.

### 2. **Reliability**
- Implemented **dead-letter queues (DLQ)** for Lambda and EventBridge to handle failures gracefully.
- Enabled **point-in-time recovery** for DynamoDB tables to ensure data durability.
- Used **Step Functions** with retries for fault-tolerant workflows.

### 3. **Performance Efficiency**
- Configured **DynamoDB** with serverless billing mode for scalable performance.
- Enabled **API Gateway caching** and throttling to optimize API performance.
- Tuned DynamoDB queries for faster data retrieval.

### 4. **Cost Optimization**
- Used **serverless services** (Lambda, DynamoDB, API Gateway) to minimize costs.
- Implemented **usage plans** in API Gateway to control and monitor API usage.
- Enabled **auto-scaling** for DynamoDB to handle varying workloads efficiently.

### 5. **Operational Excellence**
- Integrated **CloudWatch** for monitoring and logging, and **X-Ray** for tracing.
- Automated infrastructure deployment using **CloudFormation** with parameterization and tagging.
- Provided clear **API documentation** and usage plans for seamless integration.

---

## How to Deploy

### Prerequisites
- **AWS account** with appropriate permissions.
- **AWS CLI** installed and configured.

### Deployment Steps
1. Clone the repository:
   ```bash
   git clone https://github.com/shivaniuppe/FitnessTracker_IaC.git
   ```
2. Deploy the CloudFormation template:
   ```bash
   aws cloudformation create-stack --stack-name FitnessTrackerStack --template-body file://cloudformation.yaml --capabilities CAPABILITY_NAMED_IAM
   ```
3. Monitor stack creation:
   ```bash
   aws cloudformation describe-stack-events --stack-name FitnessTrackerStack
   ```
4. Test the endpoints using the provided API examples.

---

## API Usage

### Create a User
```bash
curl -X POST https://<API-ID>.execute-api.<region>.amazonaws.com/prod/users \
  -H "Content-Type: application/json" \
  -d '{"userId": "123", "email": "user@example.com", "fitnessGoal": 10000}'
```

### Get User Info
```bash
curl https://<API-ID>.execute-api.<region>.amazonaws.com/prod/users/123/user@example.com
```

### Submit Fitness Data
```bash
curl -X POST https://<API-ID>.execute-api.<region>.amazonaws.com/prod/fitness \
  -H "Content-Type: application/json" \
  -d '{"userId": "123", "email": "user@example.com", "steps": 5000}'
```

### Get Fitness Data
```bash
curl https://<API-ID>.execute-api.<region>.amazonaws.com/prod/fitness/123
```

---

## Conclusion

This project demonstrates the use of **serverless architecture** and **event-driven workflows** to build a scalable and secure fitness tracker. By adhering to the **AWS Well-Architected Framework**, the application ensures **security, reliability, performance efficiency, cost optimization, and operational excellence**. It leverages AWS services like **Lambda, DynamoDB, API Gateway, Step Functions, SNS, and Cognito** to provide a seamless user experience.

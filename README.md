# Fitness Tracker Cloud Application

## Project Overview

This project provisions a serverless fitness tracking application using AWS Cloud services, all orchestrated through Infrastructure-as-Code (IaC) using AWS CloudFormation. The core features include user management, fitness data submission, and goal tracking, with notifications triggered upon achieving fitness goals.

### Features:
1. **User Registration:** Users can register by providing their email, which is stored in a DynamoDB table.
2. **Fitness Data Submission:** Users can submit their fitness data, such as steps taken. This data is stored in another DynamoDB table.
3. **Fitness Goal Tracking:** The application tracks the user’s fitness goals, and if a user meets their fitness goal, an SNS notification is sent.
4. **API Integration:** All interactions are done through an API Gateway, which triggers AWS Lambda functions for data handling and processing.
5. **Event-Driven Notification System:** A Step Functions state machine handles notifications when fitness goals are achieved.

---

## Architecture Diagram

The overall architecture includes:
- **AWS DynamoDB** for storing user and fitness data.
- **AWS Lambda** for processing user registration, fitness data submissions, and retrievals.
- **Amazon SNS** to send notifications to users.
- **AWS Step Functions** for orchestrating events related to fitness goal achievements.
- **Amazon API Gateway** to expose APIs for client interaction.

---

## Components

### 1. DynamoDB Tables
- **Users Table:**
  - Stores user information such as `userId` and `email`.
  - Provisioned with 5 read and 5 write capacity units.
  
- **FitnessData Table:**
  - Stores fitness data with `userId` and a timestamp (`timestamp`) to track data.
  - Provisioned with 5 read and 5 write capacity units.

### 2. Lambda Functions
- **CreateUserFunction:**
  - Registers a user and subscribes them to the SNS topic for future notifications.

- **GetUserFunction:**
  - Retrieves user details from the DynamoDB `Users` table.

- **SubmitFitnessDataFunction:**
  - Submits fitness data for a user and updates the total steps count. If a fitness goal is achieved, it triggers the Step Functions workflow to notify the user.

- **GetFitnessDataFunction:**
  - Retrieves fitness data for a specific user from the `FitnessData` table.

### 3. API Gateway
Exposes RESTful endpoints for the following functionalities:
- **POST /users** – Registers a new user.
- **GET /users/{userId}/{email}** – Retrieves a user’s details.
- **POST /fitness** – Submits fitness data for a user.
- **GET /fitness/{userId}** – Retrieves a user’s fitness data.

### 4. Step Functions & SNS
- **FitnessGoalStateMachine:**
  - Triggered when a user achieves their fitness goal.
  - Sends a notification via SNS to the user’s registered email.
  
---

## How to Deploy

### Prerequisites:
- AWS account with appropriate permissions.
- AWS CLI installed and configured.

### Deployment Steps:
1. **Clone the repository:**
   ```
   git clone https://github.com/shivaniuppe/FitnessTracker_IaC.git
   ```

2. **Deploy the CloudFormation template:**
   Use the AWS CLI to deploy the provided `cloudformation.yaml` file:
   ```bash
   aws cloudformation create-stack --stack-name FitnessTrackerStack --template-body file://cloudformation.yaml --capabilities CAPABILITY_NAMED_IAM
   ```

3. **Monitor Stack Creation:**
   Use AWS CloudFormation to monitor the stack creation process:
   ```bash
   aws cloudformation describe-stack-events --stack-name FitnessTrackerStack
   ```

4. **Test the Endpoints:**
   Once the stack is created, you can interact with the API Gateway endpoints to register users, submit fitness data, and check fitness goal notifications.

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

This project demonstrates the use of serverless architecture and event-driven workflows to build a fitness tracker. Using services such as AWS Lambda, DynamoDB, API Gateway, and Step Functions, it allows users to track fitness data and receive notifications upon goal completion.

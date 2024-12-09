# AWS Image Recognition Pipeline

In this project we implement a distributed image processing pipeline using AWS services for car and text detection in images. The system uses two EC2 instances working in parallel, with AWS Rekognition for image analysis.

## Architecture Overview

The system contains:
- Two EC2 instances running Java applications
- S3 bucket for image storage
- SQS queue for inter-instance communication
- AWS Rekognition for image analysis

## Prerequisites

- AWS Account (Educate or Standard)
- AWS CLI installed
- Java 17 or higher
- Maven
- PEM key file for EC2 access(Attached)

## Setup Instructions

### 1. AWS Environment Setup

#### EC2 Instances
1. Launch two EC2 instances:
   ```bash
   Instance Type: t2.micro (free tier)
   AMI: Amazon Linux 2023
   Region: us-east-1
   ```

2. Configure Security Group:
   - Open ports: SSH (22), HTTP (80), HTTPS (443)
   - Source: Your IP ("MYIP")

3. Use the same key pair (Rekognition.pem) for both instances

#### AWS Credentials
1. Access AWS Educate account
2. Go to Vocareum and click "Account Details"
3. Copy credentials (access-key, secret-key, session-token)
4. Create credentials file on both EC2 instances:
   ```bash
   mkdir ~/.aws
   nano ~/.aws/credentials
   ```
   Note: Credentials need to be updated every 3 hours

### 2. Instance Setup

For both instances:

1. SSH into the instance:
   ```bash
   chmod 400 Rekognition.pem
   ssh -i "Rekognition.pem" ec2-user@<instance-public-dns>
   ```

2. Update system and install dependencies:
   ```bash
   sudo yum update -y
   sudo yum install java-17-amazon-corretto-devel -y
   sudo yum install maven -y
   ```

### 3. Application Setup

Attached are the two projects car-recognition.zip file for Instance A and Text Recogntion for Instance B, The files on the server must follow the project structure for the two projects

#### Instance A (Car Recognition):
1. Create project directory:
   ```bash
   mkdir -p ~/car-recognition/src/main/java/com/cs643/assignment1
   cd ~/car-recognition
   ```

2. Copy source files:
   - Copy CarRecognitionApp.java to src/main/java/com/cs643/assignment1/
   - Copy pom.xml to project root

3. Build the application:
   ```bash
   mvn clean package
   ```

#### Instance B (Text Recognition):
1. Create project directory:
   ```bash
   mkdir -p ~/text-recognition/src/main/java/com/cs643/assignment1
   cd ~/text-recognition
   ```

2. Copy source files:
   - Copy TextRecognitionApp.java to src/main/java/com/cs643/assignment1/
   - Copy pom.xml to project root

3. Build the application:
   ```bash
   mvn clean package
   ```

## Running the Applications

1. Start Instance A (Car Recognition):
   ```bash
   cd ~/car-recognition
   java -jar target/aws-recognition-1.0-SNAPSHOT.jar
   ```

2. Start Instance B (Text Recognition):
   ```bash
   cd ~/text-recognition
   java -jar target/aws-recognition-1.0-SNAPSHOT.jar
   ```

3. Check results:
   - Instance B will create output.txt with detected text
   ```bash
   cat output.txt
   ```

## Application Flow
1. Instance A processes images from S3 bucket (njit-cs-643.s3.us-east-1.amazonaws.com)
2. When cars are detected (>90% confidence), image indexes are sent to SQS
3. Instance B receives indexes and performs text recognition
4. Results are saved to output.txt

## Important Notes
- Keep EC2 instances running only while testing
- Update AWS credentials every 3 hours (Educate account)
- The S3 bucket is read-only
- Check output.txt for text recognition results

## Project Structure
```
project/
├── src/
│   └── main/
│       └── java/
│           └── com/
│               └── cs643/
│                   └── assignment1/
│                       ├── CarRecognitionApp.java
│                       └── TextRecognitionApp.java
├── pom.xml
└── README.md
```
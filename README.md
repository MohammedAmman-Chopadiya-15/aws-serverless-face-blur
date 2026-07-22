# Serverless Face Blurring with AWS

A fully automated, serverless image processing pipeline that detects and blurs faces in images using Artificial Intelligence. This project demonstrates event-driven cloud architecture, leveraging AWS services to decouple components, scale automatically, and process images without managing any underlying servers.

## Table of Contents
- [Overview](#overview)
- [Architecture and Workflow](#architecture-and-workflow)
- [Tech Stack](#tech-stack)
- [Key Features](#key-features)
- [Security and Best Practices](#security-and-best-practices)
- [Lessons Learned](#lessons-learned)

---

## Overview

In many applications, protecting user privacy by redacting faces from images is a critical requirement. This project provides a scalable, serverless solution to automate this process. When a user uploads an image, the system automatically identifies all faces using AI, applies a blur effect to protect identities, and stores the redacted image securely—all without any manual intervention.

---

## Architecture and Workflow

The system follows a highly decoupled, event-driven architecture:

1. **Image Upload (Amazon S3):** The user uploads an original image to a designated S3 source bucket.
2. **Event Decoupling (Amazon SQS):** The S3 upload triggers an event that places a message into an SQS queue. This decouples the upload process from the processing logic, ensuring no images are lost during traffic spikes.
3. **Compute and Processing (AWS Lambda):** The SQS queue triggers a Lambda function. This function downloads the image, processes it, and handles the core logic.
4. **AI Face Detection (Amazon Rekognition):** Inside the Lambda function, the AWS Rekognition API is called to analyze the image. It identifies human faces and returns precise bounding box coordinates.
5. **Image Redaction:** Using the bounding box data, the Lambda function applies a blur effect to the detected faces.
6. **Secure Storage (Amazon S3):** The final, redacted image is saved to a separate S3 destination bucket.
7. **Monitoring (Amazon CloudWatch):** All Lambda invocations, errors, and performance metrics are logged and monitored in real-time via CloudWatch.

---

## Tech Stack

| Category | Technologies and Services |
| :--- | :--- |
| **Compute** | AWS Lambda (Serverless functions) |
| **Storage** | Amazon S3 (Source and Destination buckets) |
| **Messaging** | Amazon SQS (Simple Queue Service) |
| **AI / Machine Learning** | Amazon Rekognition (Face detection and bounding boxes) |
| **Monitoring** | Amazon CloudWatch (Logs, metrics, and alerts) |
| **Programming** | Python (boto3 for AWS SDK, Pillow/OpenCV for image manipulation) |

---

## Key Features

- **100% Serverless:** No servers to provision, patch, or manage. The infrastructure scales automatically from zero to thousands of images per minute.
- **Event-Driven and Decoupled:** By using SQS between S3 and Lambda, the system is highly resilient. If the Lambda function experiences a temporary failure, the message remains in the queue for automatic retry.
- **AI-Powered Privacy:** Leverages Amazon Rekognition's pre-trained deep learning models to accurately detect faces at any angle or scale, removing the need to train custom computer vision models.
- **Automated Monitoring:** CloudWatch provides deep visibility into function execution times, memory usage, and error rates, ensuring high operational reliability.

---

## Security and Best Practices

- **Least Privilege IAM:** Each AWS service is assigned a specific IAM role with only the permissions it needs. For example, the Lambda role only has read access to the source S3 bucket, write access to the destination bucket, and permission to invoke Rekognition.
- **Data Isolation:** Original images and processed (blurred) images are stored in completely separate S3 buckets to prevent accidental overwrites and simplify access control.
- **Asynchronous Processing:** Using SQS ensures that the client uploading the image does not have to wait for the AI processing to complete, resulting in a faster user experience.

---

## Lessons Learned

Building this end-to-end serverless pipeline provided invaluable hands-on experience with cloud-native design patterns:

- **Event-Driven Design:** Learned how to properly chain AWS services using event notifications and queues to build resilient, fault-tolerant systems.
- **AI Integration:** Gained practical experience integrating managed AI services (Rekognition) into custom application logic via the AWS SDK.
- **Image Processing in Lambda:** Navigated the challenges of processing binary image data in a serverless environment, including managing memory limits and execution timeouts.
- **Debugging Distributed Systems:** Used CloudWatch Logs to trace the lifecycle of an image through S3, SQS, and Lambda, mastering the art of debugging across multiple decoupled services.

---

*Built as an advanced cloud computing project demonstrating serverless architecture, AI integration, and event-driven design on AWS.*
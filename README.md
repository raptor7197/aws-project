# Distributed Load Testing on AWS

The Distributed Load Testing Solution leverages managed, highly available and highly scalable AWS services to effortlessly create and simulate thousands of connected users generating a selected amount of transactions per second, originating from up to 5 simultaneous AWS regions. 

### DEMO LINK : https://drive.google.com/file/d/16VUPQWljXQcRD_7upi7_aVXaFoAh_dKt/view?usp=drive_link
## Architecture Overview

![Architecture](architecture.png)

Automate performance testing at scale using managed AWS services to simulate thousands of concurrent users across multiple regions without server provisioning.

## Overview

This solution enables developers to validate application behavior under load and identify bottlenecks before production deployment. It simulates user transactions from hundreds to millions of concurrent connections, testing EC2 instances, ECS/EKS clusters, Lambda functions, RDS databases, and CloudFront distributions.

## Key Features

- **Multi-Framework Support**: Run JMeter, k6, or Locust test scripts with standardized orchestration
- **Global Testing**: Simulate traffic from up to 5 AWS Regions simultaneously for realistic geographic load patterns
- **Automated Scheduling**: Schedule tests or integrate into CI/CD pipelines for continuous performance validation
- **Real-Time Monitoring**: Live dashboards via CloudWatch and IoT Core show throughput, latency, errors, and concurrency
- **Elastic Scaling**: ECS Fargate automatically scales containerized test agents without manual provisioning
- **Cost-Optimized**: Pay-per-use serverless architecture eliminates idle infrastructure costs

## Architecture

The solution uses API Gateway and Lambda for orchestration, Step Functions for workflow coordination, ECS Fargate for distributed test execution, DynamoDB and S3 for storage, Cognito for authentication, and CloudWatch for observability. A React web console provides an authenticated interface for test management.


## Source Structure

- `source/api-services` - Lambda microservices for test management via API Gateway
- `source/console` - React web application with Cognito authentication
- `source/infrastructure` - AWS CDK v2 TypeScript infrastructure definitions
- `source/task-runner` - Lambda for launching ECS Fargate tasks
- `source/task-status-checker` - Lambda for monitoring task execution
- `source/results-parser` - Lambda for aggregating test results to DynamoDB
- `source/real-time-data-publisher` - Lambda for live metrics streaming via IoT Core

## Documentation



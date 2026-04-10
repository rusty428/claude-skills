---
name: updateLambdaDocs
description: Generate or update Lambda function documentation — analyzes handler code, supporting functions, and CDK configuration to produce README.md and DEVELOPER.md. Use when a Lambda function needs documentation.
---
<!-- Version: 2026-03-08.1 -->

# Update Lambda Function Documentation

Create comprehensive documentation for a Lambda function by analyzing the handler, supporting code, and CDK configuration.

## Analysis Steps

1. **Analyze the Lambda handler file** to understand:
   - Main function purpose and workflow
   - Key operations and business logic
   - AWS services used (S3, RDS, SNS, DynamoDB, etc.)
   - Error handling patterns
   - Input/output structure

2. **Examine the Lambda directory structure** to find:
   - Supporting function files in `functions/` or `utils/` subdirectories
   - Configuration files
   - Dependencies and imports

3. **Search for CDK stack files** to identify:
   - Lambda function configuration (memory, timeout, runtime)
   - Environment variables
   - IAM permissions and policies
   - Event triggers (S3, SNS, EventBridge, API Gateway, etc.)
   - VPC configuration if applicable

4. **Look for related infrastructure** such as:
   - Database connections and schemas
   - S3 bucket configurations
   - SNS topics or SQS queues

## Output: Two Files in the Lambda's Directory

### README.md
- **Function Title & Description**: Clear title with context
- **How It Works**: Step-by-step workflow of the function's operations
- **AWS Services Integration**: Services used by this function
- **Integration Points**: What triggers this function and what it triggers next
- **Success Criteria**: What constitutes successful execution

### DEVELOPER.md
- **Technical Specifications**: Runtime, memory, timeout from CDK
- **Event Schema**: Expected input format with examples
- **AWS Services Integration**: Detailed service configurations and permissions
- **Dependencies**: Internal functions and external services
- **Environment Variables**: All required and optional env vars
- **Response Format**: Output structure and status codes
- **Error Handling**: Failure scenarios and retry logic
- **Deployment Notes**: CDK configuration and deployment details

## Style Guidelines

- **Title Format**: `FunctionName — Brief Description`
- **How It Works**: Use bulleted workflow steps
- **AWS Services**: Use **Service Name**: Role format
- **Integration Flow**: Clearly state trigger → function → next step
- **Specific Details**: Include actual function names, file paths, and configurations

## Date Stamping

Include "Last updated: YYYY-MM-DD" footer in both generated files. Run `date` to get current date.

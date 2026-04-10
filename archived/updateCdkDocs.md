---
name: updateCdkDocs
description: Generate or update CDK infrastructure documentation — analyzes stack files, resource configurations, and deployment patterns to produce README.md and DEVELOPER.md. Use when CDK infrastructure needs documentation.
---
<!-- Version: 2026-03-08.1 -->

# Update CDK Stack Documentation

Create comprehensive documentation for a CDK stack by analyzing code, resources, and project infrastructure.

## Analysis Steps

1. **Analyze CDK stack files** to understand:
   - AWS resources being deployed (Lambda, S3, SNS, RDS, DynamoDB, etc.)
   - Resource configurations (memory, timeout, permissions)
   - Inter-resource relationships and dependencies
   - Environment variables and parameters
   - IAM roles and policies

2. **Examine integrations** to identify:
   - Event triggers (S3, SNS, EventBridge, API Gateway)
   - Shared resources across stacks or functions
   - Cross-stack references and dependencies
   - Database connections and VPC configurations

3. **Review project structure** for:
   - Multiple environment configurations (dev/staging/prod)
   - Shared infrastructure components
   - External dependencies and integrations
   - Cost optimization patterns

## Output: Two Files in the CDK/Infra Directory

### README.md
- **Stack Title & Description**: Clear title with system context
- **Infrastructure Overview**: High-level architecture and purpose
- **AWS Resources Deployed**: Major resources and their roles
- **Architecture**: How components connect and communicate
- **Shared Resources**: Buckets, databases, topics used across stacks
- **Deployment Environments**: Environment-specific configurations

### DEVELOPER.md
- **Prerequisites**: Required AWS permissions, tools, and dependencies
- **Deployment Procedures**: Step-by-step CDK deployment commands
- **Environment Variables**: Stack-level and function-level configurations
- **Resource Dependencies**: What must exist before deployment
- **Environment Management**: Dev/staging/prod differences and promotion
- **Cost Considerations**: Resource sizing and cost implications
- **Troubleshooting**: Common deployment issues and solutions
- **Monitoring**: CloudWatch log locations and debugging guidance
- **Stack Updates**: How to safely modify and redeploy infrastructure

## Style Guidelines

- **Title Format**: `StackName — Infrastructure Description`
- **Resource Lists**: Use **Service Name**: Role/Purpose format
- **Architecture Flow**: Describe resource relationships and data flow
- **Deployment Commands**: Include actual CDK commands with explanations
- **Environment Awareness**: Clearly distinguish between environment configurations

## CDK-Specific Considerations

- Document construct patterns and reusable components
- Explain resource naming conventions
- Detail IAM permission strategies
- Cover VPC and networking configurations if applicable
- Note cross-region patterns (e.g., `crossRegionReferences: true`)

## Date Stamping

Include "Last updated: YYYY-MM-DD" footer in both generated files. Run `date` to get current date.

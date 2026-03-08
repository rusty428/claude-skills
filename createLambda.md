---
name: createLambda
description: Scaffold a new Lambda function with standardized structure — handler file with separation of concerns, supporting functions directory, and CDK integration. Use when adding a new Lambda function to a project.
---
<!-- Version: 2026-03-08.1 -->

# Create Lambda Function

Scaffold a standardized Lambda function with proper separation of concerns.

## Step 1: Gather Requirements

Ask the user for:

1. **Function Purpose**: What does this Lambda do? (e.g., "ProcessUpload", "SendNotification")
2. **Trigger Type**: How is it invoked? (API Gateway, S3 event, EventBridge, SQS, manual, etc.)
3. **Runtime**: TypeScript (default) or Python
4. **Memory Requirements**:
   - Light processing: 512 MB
   - Standard processing: 1024 MB (default)
   - Heavy processing: 2048 MB
5. **Timeout Requirements**:
   - Quick operations: 60 seconds
   - Standard operations: 300 seconds (default)
   - Long operations: 900 seconds

## Step 2: Create Directory Structure

### TypeScript Lambda

```
lambdas/[function-name]/
├── index.ts              # ONLY the handler function
├── functions/            # ALL business logic goes here
│   ├── process-data.ts
│   ├── validate-input.ts
│   └── format-response.ts
├── utils/                # Shared utilities (if needed)
├── package.json
└── tsconfig.json
```

### Python Lambda

```
lambdas/[function-name]/
├── handler.py            # ONLY the lambda_handler function
├── functions/            # ALL business logic goes here
│   ├── __init__.py
│   ├── process_data.py
│   ├── validate_input.py
│   └── format_response.py
└── requirements.txt
```

## Step 3: Generate Handler

The handler file contains ONLY the entry point. All business logic lives in `functions/`.

### TypeScript Handler (`index.ts`)

```typescript
import { processData } from './functions/process-data';
import { validateInput } from './functions/validate-input';
import { formatResponse } from './functions/format-response';

export const handler = async (event: any): Promise<any> => {
  console.log('Processing event:', JSON.stringify(event));

  try {
    const validationResult = validateInput(event);
    if (!validationResult.valid) {
      return formatResponse(400, validationResult.error);
    }

    const result = await processData(event);
    return formatResponse(200, result);
  } catch (error) {
    console.error('Error processing request:', error);
    return formatResponse(500, 'Internal server error');
  }
};
```

### Python Handler (`handler.py`)

```python
import json
import logging
from functions.process_data import process_data
from functions.validate_input import validate_input
from functions.format_response import format_response

logger = logging.getLogger()
logger.setLevel(logging.INFO)

def lambda_handler(event, context):
    logger.info(f"Processing event: {json.dumps(event)}")

    try:
        if not validate_input(event):
            return format_response(400, "Invalid input parameters")

        result = process_data(event)
        return format_response(200, result)
    except Exception as e:
        logger.error(f"Error processing request: {str(e)}")
        return format_response(500, f"Internal server error: {str(e)}")
```

## Step 4: Generate Supporting Functions

Create placeholder implementations in `functions/` with clear TODO markers for the user to fill in business logic.

## Step 5: Add to CDK Stack

Show the user how to wire the Lambda into their CDK stack:

```typescript
const myFunction = new lambda.Function(this, 'FunctionName', {
  functionName: 'project-function-name',
  description: 'Brief description',
  runtime: lambda.Runtime.NODEJS_22_X,  // or PYTHON_3_12
  handler: 'index.handler',             // or handler.lambda_handler
  code: lambda.Code.fromAsset('lambdas/function-name'),
  timeout: Duration.seconds(300),
  memorySize: 1024,
  environment: {
    // Add environment variables
  },
});
```

## Architecture Principles

- **handler file**: ONLY contains the entry point and imports
- **functions/**: ALL business logic, processing, and utilities
- **One function per file** for clear separation of concerns
- **Naming convention**: `[project]-[FunctionPurpose]`
- **Standard configurations**: Use the established memory/timeout tiers

## Rules

- Ask for requirements before scaffolding
- Match the project's existing runtime (TypeScript or Python)
- Follow the project's existing naming conventions
- Don't create documentation files — use `updateLambdaDocs` for that after implementation

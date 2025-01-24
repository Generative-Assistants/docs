# Workflow Engine API

This document describes how to create, configure and run Workflow Engine pipelines.

## Workflow Schema Structure

### Core structure

The Workflow configuration consists of these main components:

- Basic metadata (title, description, authors)
- OpenAPI specifications
- Input schema definition
- Workflow steps configuration

#### Required Fields

- `sentius_workflow_engine_schema_version`: Must be '0.60'
- `title`: Name of your workflow
- `description`: Brief description of the workflow's purpose
- `authors`: List of workflow authors
- `openapi`: List of OpenAPI specifications
- `input_schema`: Schema defining the expected input format
- `steps`: Ordered list of workflow steps

#### Configuration Example

```json
{
  "sentius_workflow_engine_schema_version": "0.60",
  "title": "City Information Workflow",
  "description": "Retrieves and processes city demographic information",
  "authors": "Jane Doe, John Smith",
  "openapi": [
    ...
  ],
  "input_schema": "{\"type\": \"object\", \"properties\": {\"city_name\": {\"type\": \"string\"}}, \"required\": [\"city_name\"], \"additionalProperties\": false}",
  "steps": [
    ...
  ]
}
```

### OpenAPI Configuration

OpenAPI specifications are used to define available API integrations. Each OpenAPI entry requires:

- `id` - Unique identifier for referencing the API
- `address` - URL to the OpenAPI specification

#### OpenAPI configuration example

```json
{
  "id": "upload_api",
  "address": "http://localhost:8000/openapi.json"
}
```

### Input Schema 

The input schema uses JSON Schema format to define the expected input structure. It supports:

- Property definitions
- Required field specifications
- Additional property controls
- Type validations

**PAY ATTENTION**: input schema should be in string format. Example is above in core section.

### Step Types

TODO: add step types

## Creating workflows

### Pre-requisites

We assume you have already created a dialog session and have an active session `id`. If not, please refer to the
[Dialog Sessions API](dialog-sessions.md) section.

### Validating a workflow

To check that created workflow structure is correct, run following command:

Curl

```bash
    curl -X 'POST' 'https://api.sentius.ai/workflows/forward/validate' \
      -H 'accept: application/json' \
      -H 'Content-Type: application/json' \
      -d '<YOUR_WORKFLOW_CONFIG>'
```

Python

```python
    import requests
    
    url = f"https://api.sentius.ai/workflows/forward/validate"
    response = requests.post(url, json=<YOUR_WORKFLOW_CONFIG>)
```


### Submitting a workflow

Before using workflow you must save it. After saving, you will need to save `id` field from the response - it will be
used later in workflow inference.

=== "Curl"

    ```bash
    curl -X POST "https://api.sentius.ai/dialog_sessions/{dialog_session_id}/workflows?api_key=<YOUR_API_KEY>" \
            -H "Content-Type: application/json"
            -d '<YOUR_WORKFLOW_CONFIG>'
    ```

=== "Python"
    
        ```python
        import requests
    
        url = f"https://api.sentius.ai/dialog_sessions/{dialog_session_id}/workflows"
        params = {"api_key": "your_api_key"}
        response = requests.post(url, params=params, json=<YOUR_WORKFLOW_CONFIG>)
        ```

### Using submitted workflow

Curl

```bash
    curl -X POST "https://api.sentius.ai/dialog_sessions/{dialog_session_id}/run_workflow?api_key=<YOUR_API_KEY>&workflow_id=<SAVED_WORKFLOW_ID>" \
            -H "Content-Type: application/json"
            -d '{
                "data": <YOUR_DATA>
            }'
```

Python
    
```python
    import requests
    
    url = f"https://api.sentius.ai/dialog_sessions/{dialog_session_id}/run_workflow"
    params = {"api_key": "<YOUR_API_KEY>", "workflow_id": <SAVED_WORKFLOW_ID>}
    response = requests.post(url, params=params, json={"data": <YOUR_DATA>})
```

## Example

Following config runs two actions: at first, it downloads file from Google Drive, then uploads this file to server.

### Server

Create virtual environment, activate it and run `pip install uvicorn==0.34.0 fastapi==0.115.7 fastapi==0.115.7`.

Save following code as `main.py` and start this mock server with `uvicorn main:app --port=8001`

```python
import json
import logging

from fastapi import FastAPI, File, Form, UploadFile, Request

logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s [%(levelname)s] %(message)s',
    datefmt='%Y-%m-%d %H:%M:%S'
)

logger = logging.getLogger(__name__)

app = FastAPI()


@app.post("/v1/reports/upload", status_code=201)
async def upload_report(
        file: UploadFile = File(...),
        accountId: str = Form(...),
        fileName: str = Form(...),
        testId: str = Form(...),
        isReady: bool = Form(...)
):
    logger.info("Received file upload request")
    logger.info(json.dumps(
        {
            'file_details': {
                'filename': file.filename,
                'size': file.size,
                'content_type': file.content_type
            },
            'metadata': {
                'account_id': accountId,
                'file_name': fileName,
                'test_id': testId,
                'is_ready': isReady,
            },
        }, indent=2)
    )

```

### Workflow Config

**Don't forget to put your api key value to this config before uploading.**

```json

{
  "sentius_workflow_engine_schema_version": "0.60",
  "title": "Shipping Email Processing Workflow",
  "description": "This is a workflow that processes an email and forms a basic itinerary",
  "authors": "Daniel Kornev, Fedor Ignatov",
  "openapi": [
    {
      "id": "upload_api",
      "address": "http://localhost:8001/openapi.json"
    }
  ],
  "input_schema": "{\"properties\": {\"filename\": {\"type\": \"string\"}, \"fileKey\": {\"type\": \"string\"}, \"accountId\": {\"type\": \"string\"}, \"testId\": {\"type\": \"string\"}, \"Authorization\": {\"type\": \"string\"}}, \"required\": [\"filename\", \"fileKey\", \"accountId\", \"testId\", \"Authorization\"], \"type\": \"object\", \"additionalProperties\": false}",
  "steps": [
    {
      "id": "download_policy_from_google_drive",
      "title": "Download Policy from Google Drive",
      "kind": "sentius.kinds.agents.browseragent",
      "description": "Download policy file from my Google Drive",
      "semantic_action": {
        "semantic_action_type": "browseragent",
        "structured_output_schema": "{\"properties\": {\"text\": {\"type\": \"string\"}, \"timestamp\": {\"type\": \"string\"}, \"task\": {\"type\": \"object\", \"properties\": {\"id\": {\"type\": \"integer\"}, \"dialog_session_id\": {\"type\": \"integer\"}, \"text\": {\"type\": \"string\"}, \"agent_task_id\": {\"type\": \"string\"}, \"state\": {\"type\": \"string\"}, \"is_successful\": {\"type\": \"boolean\"}, \"date_created\": {\"type\": \"string\"}, \"date_finished\": {\"type\": \"string\"}}, \"required\": [\"id\", \"dialog_session_id\", \"text\", \"agent_task_id\", \"state\", \"date_created\", \"date_finished\"]}, \"respond_buttons\": {\"type\": \"null\"}, \"task_duration_comment\": {\"type\": \"null\"}, \"attributes\": {\"type\": \"object\", \"properties\": {\"task_id\": {\"type\": \"string\"}, \"task_state\": {\"type\": \"string\"}, \"task_text\": {\"type\": \"string\"}, \"message_type\": {\"type\": \"string\"}, \"temporary_attributes\": {\"type\": \"object\", \"properties\": {\"downloadedFileName\": {\"type\": \"string\"}, \"downloadedFilePath\": {\"type\": \"string\"}}, \"required\": [\"downloadedFileName\", \"downloadedFilePath\"]}, \"finish_datetime\": {\"type\": \"string\"}, \"task_user_eval\": {\"type\": \"null\"}, \"respond_buttons\": {\"type\": \"null\"}, \"task_duration_comment\": {\"type\": \"null\"}, \"task_history_update\": {\"type\": \"null\"}}, \"required\": [\"task_id\", \"task_state\", \"task_text\", \"message_type\", \"temporary_attributes\", \"finish_datetime\"]}}, \"required\": [\"text\", \"timestamp\", \"task\", \"attributes\"], \"type\": \"object\", \"additionalProperties\": false}",
        "instruction_template": "Download %filename% from my Google Drive",
        "pods": [
          {
            "api_key": <YOUR_API_KEY>
          }
        ],
        "instruction_id": "pejWSrhXPBSpUKn"
      },
      "input_mapping": [
        {
          "source": "$context$.filename",
          "target": "%filename%"
        }
      ],
      "output_mapping": [
        {
          "source": "$output_schema$.attributes.temporary_attributes.downloadedFilePath",
          "target": "$context$.downloadedFilePath"
        },
        {
          "source": "$output_schema$.attributes.temporary_attributes.downloadedFileName",
          "target": "$context$.downloadedFileName"
        }
      ]
    },
    {
      "id": "email_preprocessing",
      "title": "Email Preprocessing",
      "description": "Preprocess the email to form basic itinerary",
      "kind": "sentius.kinds.agents.openapiagent",
      "semantic_action": {
        "semantic_action_type": "openapi.upload",
        "openapi_path": "/v1/reports/upload",
        "output_schema": "#/definitions/ReportDocument",
        "openapi_id": "upload_api",
        "pods": [
          {
            "api_key": <YOUR_API_KEY>
          }
        ]
      },
      "dependsOn": [
        "download_policy_from_google_drive"
      ],
      "input_mapping": [
        {
          "source": "$context$.downloadedFileName",
          "target": "$input_schema$.fileName",
          "type": "Sentius.Properties.Mappings.Http.Payload"
        },
        {
          "source": "$context$.accountId",
          "target": "$input_schema$.accountId",
          "type": "Sentius.Properties.Mappings.Http.Payload"
        },
        {
          "source": "$context$.testId",
          "target": "$input_schema$.testId",
          "type": "Sentius.Properties.Mappings.Http.Payload"
        },
        {
          "source": "$context$.isReady",
          "target": "$input_schema$.isReady",
          "type": "Sentius.Properties.Mappings.Http.Payload"
        },
        {
          "source": "$context$.downloadedFileName",
          "target": "$input_schema$.fileName"
        },
        {
          "source": "$context$.downloadedFilePath",
          "target": "$input_schema$.filePath"
        },
        {
          "source": "$context$.fileKey",
          "target": "$input_schema$.fileKey"
        },
        {
          "source": "$context$.Authorization",
          "target": "$input_schema$.Authorization",
          "type": "Sentius.Properties.Mappings.Http.Header"
        }
      ],
      "output_mapping": [
        {
          "source": "$output_schema$",
          "target": "$context$.reportDocument"
        }
      ]
    }
  ]
}

```

### Payload

Use following data to run workflow.

**Don't forget to change filename value**

```json
{
  "data": {
    "filename": "pavlov.txt",
    "fileKey": "file",
    "accountId": "account_id",
    "testId": "test_id",
    "Authorization": "Bearer <token>",
    "isReady": false}
}
```

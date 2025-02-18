# Workflow Engine API

This document describes how to create, configure and run Workflow Engine pipelines.

## Context

When Workflow Engine config is runned, it gets data from context, sends it to external APIs and saves responses from
these APIs to context. For steps no highest level in config (steps are elements of `steps` list of WE config) `context` -
is payload that you send to workflow. For example, if you send to WE run

```json
{
  "x": 1
}
```

This "x" could be referred in `input_mapping`s of steps as `$context$.x`.

But if step is substep of loop (see example of loop below), context will be different.

For example, let's say input is

```json
{
  "high_level_key": {
    "x": [
      {
        "y": 1
      },
      {
        "y": 2
      }
    ]
  },
  "another_high_level_key": 42
}
```

And in `loop` we set `self` as `high_level_key` and loop over `x`. In this case for every step in `sub_steps` of the
`loop` context will be elements of the list. At first iteration context will be `{"y": 1}`, at second - `{"y": 2}`.

In this case input mapping `source` should be `$context$.y`. If you need to use `another_high_level_key` value in
the one of `sub_steps`, use `$parent$`: `$context$.$parent$.$parent$.another_high_level_key`. As we said, `{"y": 1}` is
context, it is element of the list, so `$context$.$parent$` is a list. This list is the value of the key-value structure,
so `$context$.$parent$.$parent$` will be structure with `high_level_key` and `another_high_level_key`.


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

This is an example of OpenAPI configuration for the local server using 8001 port.

```json
{
  "id": "upload_api",
  "address": "http://localhost:8001/openapi.json"
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

#### Common Fields

Each step must have the following fields:

- `id`: An unique name of the step
- `title`: Step title
- `kind`: Type of the step
- `description`: Description of the step
- `semantic_action`: Schema defining step actions
- `dependsOn`: List of previous teps ids

#### Loop Step Type

Used to loop over fields in workflow run context.

- `kind`: Must be `sentius.kinds.agents.loop`
- `sub_steps`: List of the steps schemas that executed in loop
- `semantic_action`: LoopSemanticAction schema

**LoopSemanticAction**:
- `semantic_action_type`: Must be `loop`
- `self`: Optional field, describing the field of the workflow run state, that should be used as context
- `loop_over`: Field from the context that is used for iteration. Elements of this list will be contexts of sub_steps

#### Loop Example

```json
{
  "id": "step_id",
  "title": "Step Title",
  "kind": "sentius.kinds.agents.loop",
  "description": "Step Description",
  "semantic_action": {
    "semantic_action_type": "loop",
    "self": null,
    "loop_over": "field_to_loop_over"
  },
  "dependsOn": [],
  "sub_steps": [
    {
      "id": "step_id",
      "title": "Step Title",
      "kind": "sentius.kinds.agents.openapiagent",
      "description": "Step Description",
      "semantic_action": {
        "semantic_action_type": "openapi",
        "openapi_path": "/endpoint",
        "output_schema": "#/components/schemas/OutputSchema",
        "openapi_id": "testserver_openapi"
      },
      "dependsOn": [],
      "input_mapping": [
        {
          "source": "$context$.field_name",
          "target": "$input_schema$.field_name",
          "type": null
        }
      ],
      "output_mapping": [
        {
          "source": "$context$.field_name",
          "target": "$input_schema$.field_name"
        }
      ]
    }
  ]
}
```

#### PromptAgent Step Type

Used to send requests to a LLM using structured json output mode of the LLM.

- `kind`: Must be `sentius.kinds.agents.promptagent`
- `semantic_action`: PromptSemanticAction schema
- `input_mapping`: List of Mapping schemas
- `output_mapping`: List of Mapping schemas

**PromptSemanticAction**
- `semantic_action_type`: Must be `prompt`
- `system_prompt`: General prompt for LLM
- `prompt`: Specific prompt to LLM that could be use values from the input mappings
- `structured_output_schema`: The json schema of the expected output
- `structured_output_schema_name`: Name of the output schema

**Mapping**
- `source`: What field of the context should we use as input to step
- `target`: Name of the key, which uses `source` as value

#### PromptAgent Example

```json
{
  "id": "step_id",
  "title": "Step Title",
  "kind": "sentius.kinds.agents.promptagent",
  "description": "Step Description",
  "semantic_action": {
    "semantic_action_type": "prompt",
    "system_prompt": "You are a virtual agent that is an expert in geography.",
    "prompt": "Your task is to transform Country name to capital. Input: country: %country%.",
    "structured_output_schema": "{\"type\": \"object\", \"properties\": {\"name\": {\"type\": \"string\"}}, \"required\": [\"name\"], \"additionalProperties\": false}",
    "structured_output_schema_name": "PromptOutput"
  },
  "dependsOn": [],
  "input_mapping": [
    {
      "source": "$context$.field_name",
      "target": "$input_schema$.field_name"
    }
  ],
  "output_mapping": [
    {
      "source": "$context$.field_name",
      "target": "$input_schema$.field_name"
    }
  ]
}
```

#### BrowserAgent Step Type

Used for requests to Sentius Browser Agent

- `kind`: Must be `sentius.kinds.agents.browseragent`
- `semantic_action`: BrowserAgentSemanticAction schema

**BrowserAgentSemanticAction**
- `semantic_action_type`: Must be `browseragent`
- `instruction_template`: Request to Browser Agent on natural language
- `structured_output_schema`: Schema of the expected output
- `pods`: List of the Pod schemas that define on which instance of Browser Agent action should be executed
- `instruction_id`: Optional field defining id of the instruction that should be used
- `input_mapping`: List of Mapping schemas
- `output_mapping`: List of Mapping schemas

**Pod**
- `target_user_email`: Api email of the user whose Browser Agent should be used for step execution

**Mapping**
- `source`: What field of the context should we use as input to step
- `target`: Name of the key, which uses `source` as value

#### Browser Agent Example

```json
{
  "id": "step_id",
  "title": "Step Title",
  "kind": "sentius.kinds.agents.browseragent",
  "description": "Step Description",
  "semantic_action": {
    "semantic_action_type": "browseragent",
    "instruction_template": "Check the severe weather conditions for the itinerary using Sentius Browser Agent",
    "structured_output_schema": "{\"type\": \"object\", \"properties\": {\"ba_int_field\": {\"type\": \"integer\"}, \"ba_str_field\": {\"type\": \"string\"}}, \"required\": [\"ba_int_field\", \"ba_str_field\"], \"additionalProperties\": false}",
    "pods": [
      {
        "target_user_email": "email"
      }
    ],
    "instruction_id": null
  },
  "dependsOn": [],
  "input_mapping": [
    {
      "source": "$context$.field_name",
      "target": "$input_schema$.field_name"
    }
  ],
  "output_mapping": [
    {
      "source": "$context$.field_name",
      "target": "$input_schema$.field_name"
    }
  ]
}
```

#### OpenAPI Agent Step Type

Step that sends requests to API. The OpenAPI fields are:

- `kind`: Must be `sentius.kinds.agents.openapiagent`
- `semantic_action`: OpenapiSemanticAction or OpenapiUploadSemanticAction schemas
- `input_mapping`: List of OpenapiInputMapping schemas
- `output_mapping`: List of Mapping schemas

**OpenapiSemanticAction**
- `semantic_action_type`: Must be `openapi`
- `openapi_path`: Endpoint used in request
- `outpu_schema`: Reference to OpenAPI chema name
- `openapi_id`: Name of the OpenAPI schema defined in the pipeline

**OpenapiUploadSemanticAction**
- `semantic_action_type`: Must be `openapi.upload`
- `openapi_path`: Endpoint used in request
- `outpu_schema`: Reference to OpenAPI chema name
- `openapi_id`: Name of the OpenAPI schema defined in the pipeline
- `pods`: List of the Pod schemas that define on which instance of Browser Agent action should be executed

**Pod**
- `target_user_email`: Api email of the user whose Browser Agent should be used for step execution

**OpenapiInputMapping**
Note that input mapping of this step differs from the usual mapping.
- `source`: What field of the context should we use as input to step
- `target`: Name of the key, which uses `source` as value
- `type`: Must be either `Sentius.Properties.Mappings.Http.Payload`, `Sentius.Properties.Mappings.Http.Header`,
`Sentius.Properties.Mappings.Http.QueryParam` or `Sentius.Properties.Mappings.Http.PathParam`.
Values from the workflow run state will be used as payload fields, headers, query params or path params according to type values.

**Mapping**
- `source`: What field of the context should we use as input to step
- `target`: Name of the key, which uses `source` as value

#### OpenAPI Agent Example

```json
{
  "id": "step_id",
  "title": "Step Title",
  "kind": "sentius.kinds.agents.openapiagent",
  "description": "Step Description",
  "semantic_action": {
    "semantic_action_type": "openapi",
    "openapi_path": "/endpoint",
    "output_schema": "#/components/schemas/OutputSchema",
    "openapi_id": "testserver_openapi"
  },
  "dependsOn": [],
  "input_mapping": [
    {
      "source": "$context$.field_name",
      "target": "$input_schema$.field_name",
      "type": null
    }
  ],
  "output_mapping": [
    {
      "source": "$context$.field_name",
      "target": "$input_schema$.field_name"
    }
  ]
}
```

## Creating Workflows

This section contains instruction requests to validate, submit and run workflows. 

In the next Section Example, one may find an **example of workflow configuration**.

### Pre-requisites

We assume you have already created a dialog session and have an active session `dialog_session_id`. If not, please refer to the
[Dialog Sessions API](dialog-sessions.md) section.

### Validating a workflow

To check that created workflow structure is correct, run the following command:

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

Before using workflow you must submit it to Sentius API. After submitting, you will need to save `id` field from the response - it will be
used later in workflow inference.

=== "Curl"

    ```bash
    curl -X POST "https://api.sentius.ai/workflows?api_key=<YOUR_API_KEY>" \
            -H "Content-Type: application/json"
            -d '{"config": <YOUR_WORKFLOW_CONFIG>}'
    ```

=== "Python"
    
        ```python
        import requests
    
        url = f"https://api.sentius.ai/workflows"
        params = {"api_key": "your_api_key"}
        response = requests.post(url, params=params, json={"config": <YOUR_WORKFLOW_CONFIG>})
        workflow_id = response.json()["id"]
        workflow_id
        ```

### Using submitted workflow

Running workflow is connected to the particular dialog session, workflow `id` and the input data for the workflow. 
Input data must follow the input schema from the workflow configuration.
To run workflow use the following command:

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
    params = {"api_key": "<YOUR_API_KEY>", "workflow_id": workflow_id}
    response = requests.post(url, params=params, json={"data": <YOUR_DATA>})
```

## Example

Following workflow configuration runs two actions: it downloads the requested file from Google Drive, then uploads this file to the local server.
To perform the second step, one should run the local server for file uploading, or replace it with custom server 
(e.g., replace OpenAPI schema in workflow configuration).

### Server

Create virtual environment, activate it and run `pip install uvicorn==0.34.0 fastapi==0.115.7 python-multipart==0.0.20`
(requirements are written for Python 3.12).

Save following code as `main.py` and start this mock server with `python -m uvicorn main:app --port=8001`

```python
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
        request: Request,
        file: UploadFile = File(...),
        accountId: str = Form(...),
        fileName: str = Form(...),
        testId: str = Form(...),
        isReady: bool = Form(...)
):
    logger.info("Received file upload request")
    return {
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
            'authorization_header': request.headers.get('authorization'),
        },
    }
```

### Workflow Config

**Don't forget to put your Sentius API key value instead of `<YOUR_API_KEY>` to this config before uploading.**

```json

{
  "sentius_workflow_engine_schema_version": "0.60",
  "title": "File Processing Workflow",
  "description": "This is a workflow that downloads a file from Google Drive and uploads it to a server",
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
      "id": "download_file_from_google_drive",
      "title": "Download File from Google Drive",
      "kind": "sentius.kinds.agents.browseragent",
      "description": "Download file from my Google Drive",
      "semantic_action": {
        "semantic_action_type": "browseragent",
        "structured_output_schema": "{\"properties\": {\"text\": {\"type\": \"string\"}, \"timestamp\": {\"type\": \"string\"}, \"task\": {\"type\": \"object\", \"properties\": {\"id\": {\"type\": \"integer\"}, \"dialog_session_id\": {\"type\": \"integer\"}, \"text\": {\"type\": \"string\"}, \"agent_task_id\": {\"type\": \"string\"}, \"state\": {\"type\": \"string\"}, \"is_successful\": {\"type\": \"boolean\"}, \"date_created\": {\"type\": \"string\"}, \"date_finished\": {\"type\": \"string\"}}, \"required\": [\"id\", \"dialog_session_id\", \"text\", \"agent_task_id\", \"state\", \"date_created\", \"date_finished\"]}, \"respond_buttons\": {\"type\": \"null\"}, \"task_duration_comment\": {\"type\": \"null\"}, \"attributes\": {\"type\": \"object\", \"properties\": {\"task_id\": {\"type\": \"string\"}, \"task_state\": {\"type\": \"string\"}, \"task_text\": {\"type\": \"string\"}, \"message_type\": {\"type\": \"string\"}, \"temporary_attributes\": {\"type\": \"object\", \"properties\": {\"downloadedFileName\": {\"type\": \"string\"}, \"downloadedFilePath\": {\"type\": \"string\"}}, \"required\": [\"downloadedFileName\", \"downloadedFilePath\"]}, \"finish_datetime\": {\"type\": \"string\"}, \"task_user_eval\": {\"type\": \"null\"}, \"respond_buttons\": {\"type\": \"null\"}, \"task_duration_comment\": {\"type\": \"null\"}, \"task_history_update\": {\"type\": \"null\"}}, \"required\": [\"task_id\", \"task_state\", \"task_text\", \"message_type\", \"temporary_attributes\", \"finish_datetime\"]}}, \"required\": [\"text\", \"timestamp\", \"task\", \"attributes\"], \"type\": \"object\", \"additionalProperties\": false}",
        "instruction_template": "Download %filename% from my Google Drive",
        "pods": [
          {
            "target_user_email": <TARGET_USER_EMAIL>
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
      "id": "file_uploading",
      "title": "File Uploading to Server",
      "description": "Uploads file to a server given via OpenAPI schema",
      "kind": "sentius.kinds.agents.openapiagent",
      "semantic_action": {
        "semantic_action_type": "openapi.upload",
        "openapi_path": "/v1/reports/upload",
        "output_schema": "#/definitions/ReportDocument",
        "openapi_id": "upload_api",
        "pods": [
          {
            "target_user_email": <TARGET_USER_EMAIL>
          }
        ]
      },
      "dependsOn": [
        "download_file_from_google_drive"
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

**Don't forget to change filename value**. Make sure you have this file on Google Drive 
(if the extension of the file is invisible on Google Drive, write the filename without the extension).
Do not change `accountId`, `testId`, `isReady`. 
Change `Authorization` to the authorization token if you change the local server for data uploading to a custom one, 
and it requires authorization.

```json
{
    "filename": "<FILE NAME FROM GOOGLE DRIVE>",
    "fileKey": "file",
    "accountId": "account_id",
    "testId": "test_id",
    "Authorization": "Bearer <token>",
    "isReady": false
}
```

## Cleaning queues

When WE send requests to Browser Agent that don't connected to browser, it gets error, but messages are keeped in queue.
To clean the messages use following request:

```json
curl -X 'POST' \
  'https://api.sentius.ai/user/purge_queue?api_key=<YOUR_API_KEY>' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "target_user_email": "<TARGET_USER_EMAIL>"
}'
```

`target_user_emain` in payload is optional. If it's not set, queue of API_KEY owner will be cleaned.

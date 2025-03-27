<link rel="stylesheet" href="custom-style.css">

# Technical Writer Tasks API Developer Guide

## Introduction

Follow this guide to use the Technical Writer Tasks API.

### Overview 

The Technical Writer Tasks API enables you to keep track of your tasks as a technical writer.
Use the Technical Writer Tasks API to get or create tasks, including their status, across multiple components in the documentation workspace.

## Authentication

All API requests require authentication using an API key.
You must include the key in the `X-API-KEY` header for every request.

**Example header:**
```sh
X-API-KEY: your_secret_api_key_here
```

To obtain this key, contact your organization's API administrator.
Make sure not to share this key publicly.

## Endpoints

### GET /tasks
Retrieve a list of technical writing tasks with optional filtering.

- **HTTP Method:** GET
- **URI:** `https://api.techwriter.xyz/tasks`

#### Required headers

- `X-API-KEY`: Your API key.

#### Request parameters

- `status`: Filter tasks by status (OPEN, IN_PROGRESS, COMPLETED)
- `component`: Filter tasks by documentation component (API_DOCS, HELP_CENTER, SDK_DOCS, OAS_FILE)
- `updatedAfter`: Filter tasks updated after a specific date

#### Example request

```bash
curl https://api.techwriter.xyz/tasks \
  -H "X-API-KEY: your_api_key" \
  -G \
  -d "status=OPEN" \
  -d "component=API_DOCS" 
```

#### Status codes

- `200`: Successful request, an array of tasks returned.
- `401`: Authentication failed, API key is invalid.

##### Success response example (200)

```json
[
    {
      "task_id": "task_123",
      "title": "Update Gas Station Docs",
      "description": "Update Get gas station settings description.",
      "status": "OPEN",
      "component": "API_DOCS",
      "connected_tasks": []
    },
    {
      "task_id": "task_124",
      "title": "Review SDK Documentation",
      "description": "Comprehensive review of current SDK docs",
      "status": "OPEN",
      "component": "SDK_DOCS",
      "connected_tasks": []
    }
]
```

##### Error response example (401)

```json
{
  "error": "Invalid API key"
}
```

### POST /task

Create a new technical writing task.

- **HTTP Method:** POST
- **URI:** `https://api.techwriter.xyz/task`

#### Required headers

- `X-API-KEY`: Your API key.
- `Content-Type`: `application/json`

#### Request body

```json
{
  "title": "Update Gas Station Docs",
  "description": "Update Get gas station settings description.",
  "status": "OPEN",
  "component": "API_DOCS",
  "connected_tasks": ["task_id_1", "task_id_2"]
}
```

#### Example request

```bash
curl https://api.techwriter.xyz/task \
  -X POST \
  -H "X-API-KEY: your_api_key" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Update Gas Station Docs",
    "description": "Update Get gas station settings description.",
    "status": "OPEN",
    "component": "API_DOCS",
    "connected_tasks": ["task_id_1", "task_id_2"]
  }'
```

#### Status codes

- `201`: Task resource successfully created.
- `400`: Invalid task data.
- `401`: Authentication failed, API key missing or invalid.

##### Success response example (201)

```json
{
  "task_id": "task_125",
  "title": "Update Gas Station Docs",
  "description": "Update Get gas station settings description.",
  "status": "OPEN",
  "component": "API_DOCS",
  "connected_tasks": ["task_id_1", "task_id_2"]
}
```

##### Error response examples

###### **400**
```json
{
  "error": "Invalid task data"
}
```

###### **401**
```json
{
  "error": "Invalid API key"
}
```

#### Python code example
```python
import requests

API_KEY = 'your_api_key'
BASE_URL = 'https://api.techwriter.xyz'

def create_task(title, description, status, component, connected_tasks=None):
    headers = {
        'X-API-KEY': API_KEY,
        'Content-Type': 'application/json'
    }
    
    task_data = {
        'title': title,
        'description': description,
        'status': status,
        'component': component
    }

    # Only add connected_tasks if it's provided and not None
    if connected_tasks is not None:
        task_data['connected_tasks'] = connected_tasks
    
    try:
        response = requests.post(
            f'{BASE_URL}/task', 
            json=task_data, 
            headers=headers
        )
    
        response.raise_for_status()
        return response.json()

    except requests.exceptions.RequestException as e:
        print(f"Error creating task: {e}")
        return None

# Usage
new_task = create_task(
    'Update Gas Station Docs',
    'Update Get gas station settings description',
    'OPEN',
    'API_DOCS',
    connected_tasks=['task_123', 'task_124']
)
if new_task:
    print(f"New task created: {new_task}")
```

## Best practices

### API key security

- Store keys in environment variables
- Make sure to rotate API keys periodically
- Never commit API keys to version control
- Avoid sharing keys across team members

### Rate limits

- Implement exponential backoff for retries
- Cache results when possible to reduce API calls
- Contact techwriters@fireblocks.com for precise rate limit information

### Error handling

- Always check HTTP status codes
- Implement robust error handling
- Log and monitor API interactions

## Support

For additional support, contact: techwriters@fireblocks.com
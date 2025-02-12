# Tasks and Skills APIs

## Recording Skill

To create a Skill, In Sentius Copilot+ Application go to "Skills" tab and click "Record" button in the upper right corner.
One can copy ID of the Skill by clicking on "..." button of the corresponding Skill, click "Copy ID".

## Using Particular Skill

To use a particular skill, one should pass Skill ID in a task request. 
One can copy ID of the Skill by clicking on "..." button of the corresponding Skill, click "Copy ID".

=== "Python"

    ```python
    import requests
    
    url = f"https://api.sentius.ai/dialog_sessions/<dialog_session_id>/chat"
    params = {"api_key": "<your_api_key>"}
    data = {
        "text": "<task>",
        "instruction_id": "<skill_id>",
    }
    response = requests.post(url, json=data, params=params)
    print(response.json())
    ```

If the requested task was known and agent utilized recorded instruction to execute the task, in the response one can find two boolean attributes:

* `response.json()["attributes"]["temporary_attributes"]["replay_recorded_actions"]` -- whether recorded actions were replayed successfully;
* `response.json()["attributes"]["temporary_attributes"]["generated_new_actions"]` -- whether Agent had to generate new actions in general mode to perform the task.

## Retrieving Skills

### Personal Skills

To get Skills saved by the user, one can use the following code: 

=== "Curl"

    ```bash
    curl -X GET "https://api.sentius.ai/tasks/instructions?api_key=<your_api_key>" \
         -H "Content-Type: application/json"
    ```

=== "Python"

    ```python
    import requests
    
    url = "https://api.sentius.ai/tasks/instructions"
    params = {"api_key": "<your_api_key>"}
    response = requests.get(url, params=params)
    print(response.json())
    ```

### Public Skills

To get Skills available for all users, one can use the following code: 

=== "Curl"

    ```bash
    curl -X GET "https://api.sentius.ai/tasks/instructions/common?api_key=<your_api_key>" \
         -H "Content-Type: application/json"
    ```

=== "Python"

    ```python
    import requests
    
    url = "https://api.sentius.ai/tasks/instructions/common"
    params = {"api_key": "<your_api_key>"}
    response = requests.get(url, params=params)
    print(response.json())
    ```

### All Skills

To get all Skills, one can use the following code: 

=== "Curl"

    ```bash
    curl -X GET "https://api.sentius.ai/tasks/instructions/all?api_key=<your_api_key>" \
         -H "Content-Type: application/json"
    ```

=== "Python"

    ```python
    import requests
    
    url = "https://api.sentius.ai/tasks/instructions/all"
    params = {"api_key": "<your_api_key>"}
    response = requests.get(url, params=params)
    print(response.json())
    ```


## Deleting Skills

To delete Skill with ID `<skill_id>`, one can use the following code: 

=== "Curl"

    ```bash
    curl -X DELETE "https://api.example.com/tasks/instructions/<skill_id>?api_key=<your_api_key>" \
         -H "Content-Type: application/json"
    ```

=== "Python"

    ```python
    import requests
    
    url = f"https://api.example.com/tasks/instructions/<skill_id>"
    params = {"api_key": "<your_api_key>"}
    response = requests.delete(url, params=params)
    print(response.status_code)
    ```

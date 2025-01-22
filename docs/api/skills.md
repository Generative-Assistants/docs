# Tasks and Skills APIs

## Creating Skill

To create a Skill, In Sentius Copilot Application go to "Skills" tab and click "Record" button in the upper right corner.
One can copy ID of the Skill by clicking on "..." button of the corresponding Skill, click "Copy ID".

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

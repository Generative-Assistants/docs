# Tasks and Skills APIs

## Recording Skill

To create a Skill, In Sentius Copilot+ Application go to "Skills" tab and click "Record" button in the upper right corner.
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


## Copying Skills

To copy Skill with ID `<skill_id>` from the current user to the user with ID `<target_user_id>`, one can use the following code: 

=== "Curl"

    ```bash
    curl -X POST "https://api.example.com/tasks/instructions/<skill_id>/copy?api_key=<your_api_key>" \
         -H "Content-Type: application/json" \
         -d '{
           "target_user_id": "<target_user_id>",
           "target_organization_id": "<target_org_id>"
         }'
    ```

=== "Python"

    ```python
    import requests
    
    url = f"https://api.example.com/tasks/instructions/<skill_id>/copy"
    params = {"api_key": "<your_api_key>"} 
    data = {"target_user_id": "<target_user_id>", "target_organization_id": <target_org_id>}
    response = requests.post(url, params=params, json=data)
    print(response.status_code)
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

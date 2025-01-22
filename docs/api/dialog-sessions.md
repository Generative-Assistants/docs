# Dialog Sessions API

## Creating or retrieving a session

Each time you chat with Browser Agent, your message is attached to one of the dialog sessions.

You start with either getting an active session:

=== "Curl"

    ```bash
    curl -X GET "https://api.sentius.ai/dialog_sessions?api_key=<your_api_key>" \
         -H "Content-Type: application/json"
    ```

=== "Python"

    ```python
    import requests
    
    url = "https://api.sentius.ai/dialog_sessions"
    params = {"api_key": "<your_api_key>"}
    response = requests.get(url, params=params)
    print(response.json())
    ```

or creating a new one:

=== "Curl"

    ```bash
    curl -X POST "https://api.sentius.ai/dialog_sessions?api_key=<your_api_key>" \
         -H "Content-Type: application/json"
    ```

=== "Python"

    ```python
    import requests
    
    url = "https://api.sentius.ai/dialog_sessions"
    params = {"api_key": "<your_api_key>"}
    response = requests.post(url, params=params)
    print(response.json())
    ```



Either way you will receive a response containing your dialog session `<dialog_session_id>`.
Using this `<dialog_session_id>` value in the url, you can now interact with the session.


## Sending messages

To send message to the particular dialog session `<dialog_session_id>`, one may use the following code with the given arguments:

- `text` (string, **required**) - task message,

- `instruction_id` (string, optional) - one can pass the particular <skill_id> to determine the Skill which will be used to solve the task. 
If not given, agent tries to select the matching Skill among all Skills including Public and Personal Skills. 
If there is no matching Skill, agent solves the task from scratch.

- `close_tabs` (boolean, optional) - whether to close browser tabs after task completion (False, by default),

- `web_sites_for_tasks` (list of strings, optional) - list of websites to execute the task on (now we support only list of the length one),

- `utilize_user_instructions` (boolean, optional) - whether to utilize user's Skills to solve the task (True, by default).

=== "Curl"

    ```bash
    curl -X POST "https://api.sentius.ai/dialog_sessions/<dialog_session_id>/chat?api_key=<your_api_key>" \
         -H "Content-Type: application/json" \
         -d '{
           "text": "<task>",
           "instruction_id": "AeOfd32"
         }'
    ```

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


## Retrieving session history

To get the dialog history of the particular dialog session `<dialog_session_id>`, one may use the following code:

=== "Curl"

    ```bash
    curl -X GET "https://api.sentius.ai/dialog_sessions/<dialog_session_id>/history?api_key=<your_api_key>" \
         -H "Content-Type: application/json"
    ```

=== "Python"

    ```python
    import requests
    
    url = f"https://api.sentius.ai/dialog_sessions/<dialog_session_id>/history"
    params = {"api_key": "<your_api_key>"}
    response = requests.get(url, params=params)
    print(response.json())
    ```

# Whitelist API

**This API is available only for organization members with `can_use_whitelist` permission.**

## Adding user to whitelist

You can allow users to gain access to your organization and use certain features.

=== "Curl"

    ```bash
    curl -X 'POST' \
      'https://api.sentius.ai/admin/whitelist?api_key=<your_api_key>' \
      -H 'accept: application/json' \
      -H 'Content-Type: application/json' \
      -d '{
        "name": "Jane Doe",
        "email": "user@example.com",
        "permissions": [
            "can_read",
            "can_use_api",
            "can_delegate"
        ],
        "start_date": "2025-03-05T11:34:30.258Z",
        "end_date": null
        }'
    ```

=== "Python"

    ```python
    import requests
    
    url = "https://api.sentius.ai/admin/whitelist"
    params = {"api_key": "<your_api_key>"}
    data = {
        "name": "Jane Doe",
        "email": "user@example.com",
        "permissions": [
            "can_read",
            "can_use_api",
            "can_delegate"
        ],
        "start_date": "2025-03-05T11:34:30.258Z",
        "end_date": null
    }
    response = requests.post(url, json=data, params=params)
    print(response.json())
    ```


- `name` (string, **required**) - Full name.

- `email` (string, **required**) - Email address. This must be the exact address the person will use to authenticate.

- `permissions` (list, **required**) - List of permissions. Possible values are:

    - `can_read` - must be always provided to allow access to organization,

    - `can_use_api` - can use API keys,

    - `can_use_whitelist` - allow user to add or remove other users from whitelist,

    - `can_delegate` - can delegate tasks to other users

- `start_date` (datetime string, **required**) - User will be given access after this date.

- `end_date` (datetime string, optional) - User will be given access up to this date.


## Removing user from whitelist

Additionally, it is possible to revoke access to your organization.

=== "Curl"

    ```bash
    curl -X 'DELETE' \
      'https://api.sentius.ai/admin/whitelist?email=user%40example.com&api_key=<your_api_key>' \
      -H 'accept: */*'
    ```

=== "Python"

    ```python
    import requests
    
    url = f"https://api.sentius.ai/admin/whitelist"
    params = {
        "api_key": "<your_api_key>",
        "email": "user@example.com"
    }
    response = requests.delete(url, params=params)
    ```

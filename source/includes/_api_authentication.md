# Authentication

## Overview

> To authorize, use this code:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```python
import 'kittn'

api = Kittn.authorize('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

> Make sure to replace `meowmeowmeow` with your API key.


Gaining access to the Eagle Eye API is a two-stage process: Clients first present their credentials and Realm to obtain a single use Authentication Token. This single use token is valid for 30 seconds or until it has been used. Once the Authentication Token is obtained the Client must utilize it in an Authorize service call to obtain a session ID (via the "videobank_sessionid" Cookie) that provides access to resources. This two phase approach allows Clients to authenticate and operate in multiple domains. The first step is done using Authenticate. The second step is done using Authorize. Note that the Authenticate call must be done over an https connection.

Once the "videobank_sessionid" cookie is obtained from the "Authorize" call, there are 2 methods for which you can use the session ID to make subsequent calls to the API. The first, is simply to pass the "videobank_sessionid" cookie with all API requests. The second method, is to take the value of the "videobank_sessionid" cookie and pass it in the request as the "A" parameter. The "A" parameter can be used with any method (GET, PUT, POST, DELETE). The order of precedence for session ID retrieval is as follows:

1. "A" parameter in the query string of any method (GET, PUT, POST, DELETE)
2. "A" parameter in the POST data
3. "A" parameter in the request body (e.g. PUT)
4. "videobank_sessionid" cookie

All status codes are listed in order of precedence, meaning the first one listed is the one returned if its respective conditions are met, and the last one listed is the one that will be returned if none of the preceding codes' conditions are met.

## Step 1: Authenticate

## Step 2: Authorize
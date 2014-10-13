# AAA

<!--===================================================================-->
## Create Account 

This is used to create a new account and the super user for the account. As a part of the creation process, the service sends a confirmation email containing a link the user must click to activate the account. Account cannot be used until it is activated.

### HTTP Request

`POST https://login.eagleeyenetworks.com/g/aaa/create_account`

Parameter  		| Data Type   | Description   	| Is Required
---------  		| ----------- | -----------   	| -----------
**email**   	| string      | Email Address 	| true
**password**   	| string      | Password 		| true
name   			| string      | Account name
timezone   		| string      | Timezone name

### Error Status Codes

HTTP Status Code    | Data Type   
------------------- | ----------- 
400	| Unexpected or non-identifiable arguments are supplied
406	| Realm is invalid due to not being a root realm
409	| Email address has already been registered for the specified realm
202	| Account has been created and a confirmation email has been sent to the provided email address

<!--===================================================================-->
## Validate Account 

This is used to verify the email address supplied when the account is created. When successful, the account is set to active and a user session is created. User will not be required to login again.

### HTTP Request

`POST https://login.eagleeyenetworks.com/g/aaa/validate_account`

Parameter  		| Data Type   | Description   	| Is Required
---------  		| ----------- | -----------   	| -----------
**id**   		| string      | Account Id 		| true
**token**   	| string      | Account validation token | true

### HTTP Json Attributes

Parameter 	| Data Type     | Description
---------  	| -----------   | -----------
user_id 	| string 		| Unique identifier for validated user

### Error Status Codes

HTTP Status Code    | Data Type   
------------------- | ----------- 
400 | Unexpected or non-identifiable arguments are supplied
406	| Information supplied could not be verified
402	| Account is suspended
460	| Account is inactive
409	| Account has already been activated
412	| User is disabled
200	| User has been authorized for access to the realm

<!--===================================================================-->
## Forgot Password

Password recovery is a multi-step process. Step one requests a reset email be sent to the email address of a registered user. Step two validates that the reset token is valid (This step is optional but is provided to allow for a friendlier user experience). Step three uses allows the user to change the password. The results of step three is that a user session is created for the user.

### HTTP Request

`POST https://login.eagleeyenetworks.com/g/aaa/forgot_password`

Parameter  		| Data Type   | Description   	| Is Required
---------  		| ----------- | -----------   	| -----------
**email**   	| string      | Email Address 	| true

### Error Status Codes

HTTP Status Code    | Data Type   
------------------- | ----------- 
400 | Unexpected or non-identifiable arguments are supplied
406	| Information supplied could not be verified
402	| Account is suspended
460	| Account is inactive
461	| Account is pending
412	| User is disabled
462	| User is pending
202	| An reset email has been sent to the supplied email address. This status will be provided even if the email address was not found. This prevents attacks to discover user accounts.

<!--===================================================================-->
## Check Password Reset Token

This is step two of the password recover/reset process. It verifies that the supplied token is a valid reset token.

### HTTP Request

`POST https://login.eagleeyenetworks.com/g/aaa/check_pw_reset_token`

Parameter  		| Data Type   | Description   	| Is Required
---------  		| ----------- | -----------   	| -----------
**token**   	| string      | Password reset token provided in email | true

### Error Status Codes

HTTP Status Code    | Data Type   
------------------- | ----------- 
400 | Unexpected or non-identifiable arguments are supplied
406	| Token not valid or not found
402	| Account is suspended
460	| Account is inactive
461	| Account is pending
412	| User is disabled
202	| Token is valid

<!--===================================================================-->
## Reset Password

This is step three of the password recover/reset process. It both verifies that the supplied token is a valid reset token and then, if valid resets the password associated with the token to the newly supplied password. Upon completion, a user login session is created.

### HTTP Request

`POST https://login.eagleeyenetworks.com/g/aaa/reset_password`

Parameter  		| Data Type   | Description   	| Is Required
---------  		| ----------- | -----------   	| -----------
**token**   	| string      | Password reset token provided in email | true
password   		| string      | New password | true

### HTTP Json Attributes

Parameter 	| Data Type     | Description
---------  	| -----------   | -----------
user_id 	| string 		| Unique identifier for validated user

### Error Status Codes

HTTP Status Code    | Data Type   
------------------- | ----------- 
400 | Unexpected or non-identifiable arguments are supplied
406	| Token not valid or not found
402	| Account is suspended
460	| Account is inactive
461	| Account is pending
412	| User is disabled
200	| User has been authorized for access to the realm

<!--===================================================================-->
## Resend Registration Email

This is used by users who have registered for an account, but never confirmed the registration. This will allow the registration confirmation email to be re-sent to the user.

### HTTP Request

`POST https://login.eagleeyenetworks.com/g/aaa/resend_registration_email`

Parameter  		| Data Type   | Description   	| Is Required
---------  		| ----------- | -----------   	| -----------
**email**   	| string      | Email address of the account contact for a pending account | true

### Error Status Codes

HTTP Status Code    | Data Type   
------------------- | ----------- 
400 | Unexpected or non-identifiable arguments are supplied
404	| Account with this email address and realm could not be found
402	| Account is suspended
460	| Account is inactive
409	| Account is already active (not pending)
412	| User is disabled
202	| Account was located and verified to be in the pending state. A registration email has been recreated and sent to the provided email address.

<!--===================================================================-->
## Resend User Verification Email

This is used by users who have had a user account created for them, but they never confirmed their user account. This will re-send the user confirmation email so that they can then confirm their user account.

### HTTP Request

`POST https://login.eagleeyenetworks.com/g/aaa/resend_user_verification_email`

Parameter  		| Data Type   | Description   	| Is Required
---------  		| ----------- | -----------   	| -----------
**email**   	| string      | Email address of the new user | true

### Error Status Codes

HTTP Status Code    | Data Type   
------------------- | ----------- 
400 | Unexpected or non-identifiable arguments are supplied
404	| User with this email address and realm could not be found
402	| Account is suspended
460	| Account is inactive
461	| Account is pending
412	| User is disabled
409	| User is already active (not pending)
202	| User was located and verified to be in the pending state. A verification email has been recreated and sent to the provided email address.

<!--===================================================================-->
## Change Password

This allows a user to change their password directly while authenticated, and also allows super users to change the password of the users they manage. If someone is changing their own password, they must send their current password as well. If someone is changing one of the users they manage, they only need to send the new password.

### HTTP Request

`POST https://login.eagleeyenetworks.com/g/aaa/change_password`

Parameter  		| Data Type   | Description   	| Is Required
---------  		| ----------- | -----------   	| -----------
id   			| string      | ID of the user having their password changed. Optional. Defaults to the ID of the authenticated user. If empty or equal to authenticated user, then "current_password" becomes required. | 
**password**   	| string      | New password | true
current_password| string      | Current password of the user. Optional. If "id" argument is empty, or is equal to the authenticated user's id, then this is required. | true

HTTP Status Code    | Data Type   
------------------- | ----------- 
401 | Unauthorized due to invalid session cookie
400	| Unexpected or non-identifiable arguments are supplied
404	| User with the "id" provided cannot be found
406	| The "current_password" provided does not match the password of the authenticated user
200	| User password was changed successfully

<!--===================================================================-->
## Switch Account

This allows a user to "log in" to another account that the user has access to (see "list/accounts"). Most commonly this be would be needed for a master account user accessing their sub accounts.

### HTTP Request

`POST https://login.eagleeyenetworks.com/g/aaa/switch_account`

Parameter  		| Data Type   | Description   	| Is Required
---------  		| ----------- | -----------   	| -----------
account_id   	| string      | ID of the account to login to. Optional. Defaults to the account ID that the user belongs to. | false

HTTP Status Code    | Data Type   
------------------- | ----------- 
401 | Unauthorized due to invalid session cookie
400	| Unexpected or non-identifiable arguments are supplied
404	| Account with the "account_id" provided cannot be found
200	| Account context switch successful

<!--===================================================================-->
## Logout

Log out user and invalidate HTTP session cookie

### HTTP Request

`POST https://login.eagleeyenetworks.com/g/aaa/logout`

HTTP Status Code    | Data Type   
------------------- | ----------- 
204 | User has been logged out
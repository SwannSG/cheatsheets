## OAuth 2.0 Services



### Roles

Resource owner
 - the user

Resource server
 - server hosting protected data

Authorisation server
 - verifies the identity of the user
 - issues access tokens to the *application* 
 - This is the server that presents the interface where the user approves or denies the request.

Client application
 - application that want's access tto the user's account

 - Service that hosts the user account
    - username
    - password
    - userid

### Bride Application


User registered with Google (Google Identity Platform)

Bridge application requires user to register.
 - register using existing Google user
 or
 - register a new user on User Account Service
    - username/ password
    - can authenticate user 

(Google Sign-In For Websites)[https://developers.google.com/identity/sign-in/web/sign-in]

(Google Sign-In For Websites Videos)[https://www.youtube.com/watch?annotation_id=annotation_673138331&feature=iv&index=2&list=PLNYkxOF6rcIBQCKXOfi4AUtSpMj78pX5f&src_vid=Oy5F9h5JqEU&v=j_31hJtWjlw&ab_channel=GoogleChromeDevelopers]

Sign in with:
 - Google
 - Facebook
 - Twitter
 - Microsoft
 - Yahoo
 - Email

Called federated login


1. Browser (sign-in) ----------------> Google (authentication)
2. Browser (signed-in, id_token) <---- Google (authentication)
3. Browser (id_token) ----------> Server (extract user profile from id_token)
---
template: templates/complete-guide.html
heading: Accessing protected API from your React app
read_time: 2 min
---

In this section, we will focus on how to call a secure API from your Next.js app.

We’ve already covered the key steps for adding user login and managing authentication in your Next.js app. To recap, during user login, the auth.js library provides both an ID token and an access token. So far, we've been using the ID token to establish the logged-in user's context and enable secure access to protected routes. Now, let's shift our focus to the access token, which is crucial for calling secure APIs from your Next.js app.
The access token is typically used when your application needs to interact with a secure backend API. This token contains the necessary permissions (or "scopes") for making API requests on behalf of the authenticated user. In this section, we’ll explore how to use this token to make authenticated API calls from your Next.js app.

For simplicity, let's assume that the APIs you're calling are secured by the same Identity Provider (IdP) and share the same issuer—in this case, the same Asgardeo organization. This setup is common when your Next.js app is interacting with internal APIs that belong to the same organization. However, if your app needs to call APIs secured by a different IdP, you’ll need to exchange your current access token for a new one issued by the IdP securing those APIs. This can be done using the OAuth2 token exchange grant type or other supported grant types. We will cover these scenarios in a separate guide. 

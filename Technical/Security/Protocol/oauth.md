# OAuth
## Definition
- OAuth is a security standard where you give one application permission to access _your data_ in another application. The steps to grant permission, or _consent_, are often referred to as _authorization_ or even _delegated authorization_. You authorize one application to access your data, or use features in another application on your behalf, without giving them your password.
- OAuth 2.0 is designed only for *authorization*, for granting access to data and features from one application to another.
##  Terminologies
### Resource Owner
- The owner of the identity, data, and any actions can be performed on the owner account.
### Client 
- The application that wants to access data or perform actions on behalf of the **Resource Owner**.
### Authorization server
- The application that knows the **Resource Owner**, where the **Resource Owner** already has an account.
### Resource server
- The API or service the client wants to use on behalf of the **Resource Owner**.
### Redirect URL
- The URL the authorization server will redirect the resource owner back to after granting permission to the client, which is sometimes referred to as the *Callback URL*.
### Response type
- The type of information the client expects to receive, The most common **Response Type** is **Code**, where the **Client** expects an **Authorization Code**.
### Scope
- These are the granular permission the client wants, such as access to data or to perform actions.
### Consent
- The **Authorization Server** takes the **Scopes** the **Client** is requesting, and verifies with the **Resource Owner** whether or not they want to give the client permission.
### Client ID
- This ID is used to identify the client with the **Authorization Server**.
### Client secret
- This is a secret password that only the **Client** and **Authorization Server** know. This allows them to securely share information privately behind the scenes.
### Authorization code
- A short-lived temporary code the client give the **Authorization Server** in exchange for an **Access Token**.
### Access token
- Strictly for authorization.
- It allows a client application to access a specific resource to perform specific actions on behalf of the user, which is known as a **delegated authorization scenario** where user delegates a client application to access a resource on their behalf.
- The key the client will use to communicate with the **Resource Server**. This is like a badge or key card that give the **Client** permission to request data or perform action with the **Resource Server** on your behalf.
- Understanding how to retrieve the needed information to make authorization decisions is an agreement between the authorization server and the resource server.
- With the access token, there is a set of techniques, collectively known as _sender constraint_, that allow you to bind an access token to a specific sender. This guarantees that even if an attacker steals an access token, they can’t use it to access your API since the token is bound to the client that originally requested it.
![[Pasted image 20250127142541.png]]
```ad-note
Sometimes the **Authorization Server** and the **Resource Server** are the same server. However, there are cases where they will _not_ be the same server or even part of the same organization. For example, the **Authorization Server** might be a third-party service the **Resource Server** trusts.
```
## OAuth Flow
![[Pasted image 20250127114632.png]]
1. You, the **Resource Owner**, want to allow “Terrible Pun of the Day,” the **Client**, to access your contacts so they can send invitations to all your friends.
2. The **Client** redirects your browser to the **Authorization Server** and includes with the request the **Client ID**, **Redirect URI**, **Response Type**, and one or more **Scopes** it needs.
3. The **Authorization Server** verifies who you are, and if necessary prompts for a login.
4. The **Authorization Server** presents you with a **Consent** form based on the **Scopes** requested by the **Client**. You grant (or deny) permission.
5. The **Authorization Server** redirects back to **Client** using the **Redirect URI** along with an **Authorization Code**.
6. The **Client** contacts the **Authorization Server** directly (does not use the **Resource Owner**’s browser) and securely sends its **Client ID**, **Client Secret**, and the **Authorization Code**.
7. The **Authorization Server** verifies the data and responds with an **Access Token**.
8. The **Client** can now use the **Access Token** to send requests to the **Resource Server** for your contacts.
## References
- https://developer.okta.com/blog/2019/10/21/illustrated-guide-to-oauth-and-oidc
- https://auth0.com/blog/id-token-access-token-what-is-the-difference/
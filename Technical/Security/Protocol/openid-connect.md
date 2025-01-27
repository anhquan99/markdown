# OpenID Connect
- OpenID Connect (OIDC) is a thin layer that sits on top of [[oauth|OAuth 2.0]] that adds login and profile information about the person who is logged in.
- Establishing a login session is often referred to as _authentication_, and information about the person logged in (i.e. the **Resource Owner**) is called _identity_. 
- When an Authorization Server supports OIDC, it is sometimes called an _identity provider_, since it _provides_ information about the **Resource Owner** back to the **Client**.
- OpenID Connect enables scenarios where one login can be used across multiple applications, also known as _single sign-on_ (SSO).
## OpenID Connect flow
- It is the same as [[oauth#OAuth Flow|OAuth flow]], only differences are, initial request, a specific scope of `openid` is used, and in the final exchange the **Client** receives both an **Access Token** and an **ID Token**.
![[Pasted image 20250127121538.png]]
### ID Token
- Strictly for authentication, it proves the user has been authenticated.
- An **ID Token** is a specifically formatted string of characters known as a JSON Web Token (JWT).
- The **Client** can extract information embedded in the JWT such as your ID, name, when you logged in, the **ID Token** expiration, and if anything has tried to tamper with the JWT. The data inside the **ID Token** are called _claims_.
- The ID token is signed by the issuer with its private key. This guarantees you the origin of the token and ensures that it has not been tampered with. You can verify these thing by using the issuer's public key.
- Compare to [[oauth#Access token|access token]].
![[Pasted image 20250127143802.png]]
### Claims
- `aud`: audience, which is the client ID of the application that should consume the token.
- `sub`: unique ID of the user (identity).
- `iss`: identity provider who provide the token.
![[Pasted image 20250127140620.png]]
## References
- https://developer.okta.com/blog/2019/10/21/illustrated-guide-to-oauth-and-oidc
- https://auth0.com/blog/id-token-access-token-what-is-the-difference/
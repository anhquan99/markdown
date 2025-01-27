# Components
## Services
### Profile service
- Encapsulates retrieval of user claims from a data source of your choice.
## Endpoints
### Authorization endpoint
- The authorize endpoint can be used to request tokens or authorization codes via the browser. This process typically involves authentication of the end-user and optionally consent.
### # UserInfo endpoint
- Used to retrieve claims about a user.
- The caller needs to send a valid access token. Depending on the granted scopes, the UserInfo endpoint will return the mapped claims.
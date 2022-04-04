# Claim 
- Claim based authorization, checks the value of claim and allows acces to a resource based upon that value.
- For example, if you want access to a nightclub, the authorization process might be
	- The door security officer would evaluate the value of the date of birth claim and whether they trust the issuer (the driving license authority) before granting you access
# Role based
- Each user include their own role in the system and when user accesses, using that role system will or will not give user access. 
# Policy based
- Underneath the covers, role-based authorization and claims-based authorization use a requirement, a requirement handler, and a preconfigured policy.
# Principal
Represent a subject via a collection of claims.
# Identity cookie
- Track to which user a request belongs to
- Safety stores user information
- Symmetric encryption, key only on server
- Used tp re-construct the ClaimsPrincipal object on each request
- Secured by ASP.NE Core Data Protection
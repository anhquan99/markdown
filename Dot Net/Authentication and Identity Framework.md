# Claim 
- Claim based authorization, checks the value of claim and allows acces to a resource based upon that value.
- Claim is the information about the subject. It represents what the subject is, not what subject can do.
- For example, if you want access to a nightclub, the authorization process might be
	- The door security officer would evaluate the value of the date of birth claim and whether they trust the issuer (the driving license authority) before granting you access
- 3 standard claims:
	- nameidentitfier: user id
	- name: username
	- amr - authentication method reference
# Security stamp
- When user login into the system, it will create and pass security stamp to user cookie. When user change password or make modify on the account, this security stamp will be changed and user can't use the old security stamp to access.
# Role based
- Each user include their own role in the system and when user accesses, using that role system will or will not give user access. 
- # Role and Role Claims
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
# Scheme actions
- **Authenticate:** How claim and principal is created
- **Challenge:** Define what happen if user access url or resources that need to authorize.
- **Forbid:** Define what happen if user access forbiden url or resources.
# Claim identity
- `ClaimsIdentity` is a class contains claims, it describle the entity that the corresponding identity represents, and can be used to make authorization and authentication decisions.
```ad-danger
The **ClaimsPrincipal** class has a Claims property as well. In the majority of cases you should access the user's claims through the ClaimsPrincipal.Claims collection rether than through the **Claims** collection. You will need to access the claims of an individual **ClaimsIdentity** only in the cases wherer the principal contains more than one **ClaimsIdentity** and you need to evaluate or modify a specific identity.
```
```ad-important
To add or remove claims from the **Claims** collection, a caller must have full trust.
```
# Claim principal
- The principal object represents the security context under which code is running. Applications that implement role-based security grant rights based on the role associated with a principal object.
- The `ClaimsPrincipal` in context will always have more than 1 identity if your application requires n factor authentication (n > 1)
``````
Principal = User
Identity = Driver's License, Passport, Credit Card, Google Account, Facebook Account, RSA SecurID, Finger print, Facial recognition, etc.
``````
- If you are pulled over by the police, they don't verify you're who claim to be, based on your driver's license alone. They also need to see your face. Otherwise, you could show anyone driver's license.
-  Hence, it makes sense, why authentication can and sometimes should be based on multiple identities.
# Identity framework use SHA256 to hash password
# `UserClaimsPrincipalFactory` 
# Send mail
- Lib:
	- SendGrid


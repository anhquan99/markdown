# SSL Certificates
- SSL stands for **secure sockets layer**.
- TLS stands for **transport layer security**.
- Usage of certificate:
	- Authenticate legitimate object
	- Encrypt network trafic
## Openssl usage
- Creation and management of private keys, public keys and parameters
- Public key cryptographic operations
- Creation of X.509 certificates, CSRs and CRLs
- Calculation of Message Digests and Message Authentication Codes
- Encryption and decryption with Ciphers
- SSL/TLS client and server tests
- Handling of S/MIME signed or encrypted mail
- Timestamp requests, generation and verification
## Certificate Signing request (CSR)
- Example scenario: user visit a website, their browser needs to know the certificate of that website is legitimate with the certificate authority (ex: google, ...). In that case, the website must register the certificate with the certificate authority by using a **certificate signing request**.
- After the CSR is signed, the website certificate is legitimate.
- In the process of signing the certificate, the result is 2 files: private key and CSR.
```shell
openssl req -newkey rsa:2048 -keyout key.pem -out req.pem
```
## Manage SSL certificates
```shell
# generate self sign certificate
openssl req -x509 -noenc -newkey rsa:4096 -days 365 -keyout self_sign.key -out self_sign.crt

# to view certificate
openssl x509 -in certificate.crt -text
```
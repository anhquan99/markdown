# LDAP
- Stand for Lightweight Directory Access Protocol, which is derivative of x.500 specification for providing directory services via the IP protocol.
- LDAP provides database-style functionality specifically engineered to be fast, computationally lightweight, and robust. The LDAP application protocol details transport, directory layout (organization), and methods of access to the data for operations such as read, write, delete, etc.
- LDAP is used in many SSO and Identity Management solution.
## Authentication
- LDAP is an industry standard protocol for using and administering distributed directory services over the network, and is meant to be both open and vendor-neutral.
- LDAP is TLS and SSL aware. TLS/SSL will provide a secure encrypted connection.
- The client only need to specific a minimum amount of information to connect to an LDAP server.
- `openldap-clients` package is used for programs.
- PAM is a pluggable authentication module, and it is the default authentication mechanism for Linux. PAM is configured to use the `pam.sss.so` module.
- `sss.so` and `sssd` use a combined `sssd` and LDAP configuration file.
![[image.png]]
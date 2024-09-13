# Project-3-LDAP

To configure an LDAP (Lightweight Directory Access Protocol) server on RHEL or CentOS, you can follow these steps. This guide will help you set up an LDAP server, which is often used for centralized authentication.

# Step 1: Install OpenLDAP Server and Utilities
Update your system:


sudo dnf update
Install OpenLDAP server and utilities:


sudo dnf install openldap-server openldap-clients


<img width="459" alt="ld" src="https://github.com/user-attachments/assets/6554f902-c155-4c4b-b81b-4b11f36a8c7f">


Step 2: Configure the OpenLDAP Server
Start and enable the OpenLDAP service:

sudo systemctl start slapd
sudo systemctl enable slapd

<img width="451" alt="jj" src="https://github.com/user-attachments/assets/ed5d5419-431b-40da-a8f5-d59e934b5f87">

Configure the OpenLDAP server:

OpenLDAP uses configuration files located in /etc/openldap/slapd.d/. You need to create a configuration directory if it doesn't exist:


sudo mkdir -p /etc/openldap/slapd.d

<img width="364" alt="45" src="https://github.com/user-attachments/assets/a8b264cf-7f66-418e-a4e5-0c642eb71ec9">

Create an initial configuration file:

OpenLDAP uses the cn=config configuration backend. To set it up, you need to create an LDIF file to initialize the directory structure.

Create a file called init.ldif:

sudo nano init.ldif

Add the following content, replacing dc=example,dc=com with your desired domain components:

dn: dc=example,dc=com
objectClass: dcObject
objectClass: organization
dc: example
o: Example Inc

dn: cn=admin,dc=example,dc=com
objectClass: organizationalRole
cn: admin
description: LDAP Administrator

dn: ou=People,dc=example,dc=com
objectClass: organizationalUnit
ou: People

dn: ou=Groups,dc=example,dc=com
objectClass: organizationalUnit
ou: Groups
Add the initial configuration to the directory:

<img width="204" alt="87" src="https://github.com/user-attachments/assets/8f29b813-6150-4a54-9302-f8c17a41d88e">


ldapadd -Y EXTERNAL -H ldapi:/// -f init.ldif

<img width="411" alt="88" src="https://github.com/user-attachments/assets/6391d189-7ca8-4c3d-9bc8-6d69af6dd4d6">


Step 3: Configure Access Control
Edit the access control list (ACL):


Create or modify the olcAccess entries in the cn=config configuration.

Create a file called acl.ldif:

sudo nano acl.ldif
Add the following content:


dn: olcDatabase={1}mdb,cn=config
add: olcAccess
olcAccess: to attrs=userPassword by self write by anonymous auth by dn.base="cn=admin,dc=example,dc=com" write by * none
olcAccess: to * by self write by dn.base="cn=admin,dc=example,dc=com" write by * read

<img width="459" alt="99" src="https://github.com/user-attachments/assets/df8e13dc-7bc8-4d43-bdc0-c35ec3e92409">



Apply the ACL configuration:


ldapmodify -Y EXTERNAL -H ldapi:/// -f acl.ldif

# Step 4: Test the LDAP Server
Add sample data to the directory:

Create a file called sample.ldif:

sudo nano sample.ldif
Add sample user data:


dn: uid=jdoe,ou=People,dc=example,dc=com
objectClass: inetOrgPerson
uid: jdoe
cn: John Doe
sn: Doe
userPassword: password

<img width="252" alt="uu" src="https://github.com/user-attachments/assets/1a45be58-db46-4b7c-9766-0cf91d0e6c25">

Add the sample data to the directory:

ldapadd -x -D "cn=admin,dc=example,dc=com" -W -f sample.ldif
<img width="457" alt="24" src="https://github.com/user-attachments/assets/d2009d63-df4f-4da2-8960-b1dbe2114f6a">


Search the LDAP directory:

Verify the directory contents:


ldapsearch -x -b "dc=example,dc=com"

<img width="232" alt="56" src="https://github.com/user-attachments/assets/452e9d37-576a-44bf-96b7-bbaedfc29b67">



# Step 5: Configure LDAP Client (Optional)

If you need to connect clients to your LDAP server, you’ll need to install and configure LDAP client utilities on those systems.

Install LDAP client utilities:


sudo dnf install openldap-clients


<img width="457" alt="hy" src="https://github.com/user-attachments/assets/ca11d3f6-7e73-40ce-b471-bf12636f438b">


Configure the LDAP client:

Edit the LDAP client configuration file, usually located at /etc/openldap/ldap.conf. Add your LDAP server details:


BASE    dc=example,dc=com
URI     ldap://your-ldap-server-ip

<img width="428" alt="09" src="https://github.com/user-attachments/assets/94de629a-50fc-4295-8fff-dff0ea897949">


Test the LDAP client:

Use ldapsearch to query the LDAP server from the client:

ldapsearch -x

<img width="149" alt="51" src="https://github.com/user-attachments/assets/42911a55-e7f2-4640-8b51-8b1beeb9fe79">


# Conclusion
You now have a basic LDAP server configured on CentOS 8. You can customize it further by adding more entries, adjusting ACLs, and integrating it with other services as needed. For production environments, consider securing your LDAP traffic with TLS/SSL and setting up proper backups and monitoring.







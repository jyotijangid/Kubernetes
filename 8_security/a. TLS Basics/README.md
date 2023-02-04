TLS Transport Layer Security is achieved by TLS Certificates.

TLS Certificates:
    
- Guaranetees trust between two parties during a session. The communication between the user & server is encrypted & server is actaully the server it claims to be. 

- SYMMETRIC ENCRYPTION: The information is encrypted with a key and sender sends it to the receiver along with the key(for decryption) over the network a third party can easily access the data transferred (as encrypted info & key both are available). So, symmetric encryption is that when same key is used for encryption as well as decryption. This is not very secure. We need to securely pass the key from sender to receiver in this case.  
- ASYMMETRIC ENCRYPTION: A public & private key pair are generated on server (one key encrypts & other key decrypts). When browser hits the URL it gets the PUB kye from server. Browser generats the SYMM Key. The SYMM Key is encrypted with the PUB Key and send to the server over internet. The server gets PUB+SYMM encrypted which is decrypted using PRI Key and server gets the SYMM Key. When User sends data encryoted with SYMM Key (data+SYMM) to the internet, server is able to decrypt it using the SYMM Key. Here we securly passed SYMM Key from Client to Server without letting third party access it. 
- ADMISTRATOR generates the key pair to enable SSH Connections: The public key is stored in server's `.ssh/authorized_keys` folder & using the private key we can access the server.

```
1. >>> ssh-keygen -t rsa -d 1028
2. store the pub key in .ssh/authorized_keys folder of Server
3. >>> ssh -i private.key user@Server --- access the server
```            
- SERVER (WEB SERVER) - Generate key pair for secure connection over https. (PUB + PRI)
```
>>> openssl genrsa -out private.key 1024 ---- generate the private key
>>> openssl rsa -in private.key -pubout > public.pem ----- generates the public key with private.key
```
- When we hit a URL we get a CERT from the server signed by CA which is: CERT is PUB Key of Server + PRI Key of CA. The PUB Key of Server is decrypted using CA's Public Key which is already stored in the browser. If the browser suspects the CERT is invalid it warns as NOT SECURE. Anybody can sign the certificate (self-signed) but they are not a CA. CA i.e. digicert, symantec, globalsign.  
- In order to generate a CERT for a Site, the Server sends CSR(Certificate Signing Request) request to CA with the PUB Key. The CA then validates & signs the CSR using the CA's PRI Key. (CA has it's set of PUB & PRI Key).
- A CSR (Certificate Signing Request) (file.csr) is generated using openssl along with the PUB key generated of server & domain name/other details. This file.csr is passed to the CA, they validate the certificate request. Finally they sign & send the certificate back.  
```
>>> openssl req -new -key private.key -out file.csr -subj "/C=US/ST=CA/O=MyOrg, Inc./CN=mydomain.com" ----- generates a CSR
```
- How does the browser know that the CA is legitimate? CA has public & private key as well, while signing the certificate they use the private key & the public key is built in with the browser.  
- For an organization, they can host their own private on premise CAs & have the PUB key installed in the browser of all the machines of their organization. 

- All this above validates the server but how does the server know if the client is who say he is.  
- Client also generates the certificates with CA & pass it to the server. But this is implemented under the hoods so a normal user don't have to generate & manage the certificates on their own. 
- PKI - Public Key Infrastructure: The whole system of CAs, Servers, people, process of generating, distribution & maintaining certificates is PKI. 
- Data encrypted with a private key can be decrypted by the people who have the public key & vice versa. 


1. Admin generates keys for SSH Connections.
2. Web Server generates Keys to secure website over internet. (Client is able to validate the legitimacy of the Server)
3. CA comes into picture for the 2.
4. End Users only generate SYMM Key(ggenerated on Browser) to connect to a SERVER.

Server's authentication by Client is established using Keys but Server doesn't know whether the Client is who they say they are. 
This is achieved with Client Certificates (Clients also generates CSR & get it signed by CA), thus server can valiadate. But this is generally not done even when Client Certificates are generated it all happens under the hood by browser.

LDAP: Lightweight Directory Access Protocol: is an open and cross platform protocol used for directory services authentication.

#####WAYS HACKER CAN HACK?
1. Accessing the data a user sends over the Internet.
2. Pretending to be the Server a user wants to access & making you enter your data into their site.

#####HOW TO LOOK AT CERT & SAY IT IS LEGITIMATE? 
Check who signed it - is it a Authorized CA or is iit Self-Signed. 
SELF-SIGNED: e.g. I'll sign the certificate but it is not safe as I'm not authorized. 
CAs: like Digicert,symmantec etc.

####NOTE
- Public Key: *.crt, *.pem         
- Private Key: *.key, *-key.pem
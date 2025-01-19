# SSH  
OpenSSL is a cryptographic library that enables an open source implementation of Transport Layer Security (TLS) and Secure Sockets Layer (SSL) protocols. It provides functions to generate private keys, manage certificates, and equip client applications with encryption and decryption.  



## Generate keys  
Create SSH keys using `ECDSA` algorithm.
- Elliptic Curve Digital Signature Algorithm (ECDSA) is a Digital Signature Algorithm (DSA) which uses keys derived from elliptic curve cryptography (ECC).
- ECDSA is one of the more complex public key cryptography encryption algorithms.  



1. **Public key**  
```sh
openssl ecparam -genkey -name prime256v1 -noout -out /path/to/ecdsa_private_key_name.pem
```

2. **Public key**  
```sh
openssl ec -in /path/to/ecdsa_private_key_name.pem -pubout -out /path/to/ecdsa_public_key_name.pem
```

3. **Verify generated keys**  
```sh
openssl ec -in /path/to/ecdsa_private_key.pem -check
``` 


4. **Convert Public Key to OpenSSH format**  
Servers requires public key in `OpenSSH` format for authentication.  
```sh
ssh-keygen -f /path/to/ecdsa_public_key_name.pem -i -m PKCS8 > /path/to/public_key_openssh.pub
``` 


## Copy Public key to the server  
1. Use `ssh-copy-id` to automatically copy the public key.  
```sh
ssh-copy-id -i /path/to/your/key.pub user@server_ip
```  

2. Copy public key using `OpenSSH` format manually.  
```sh
cat /path/to/ecdsa_public_key_openssh.pub
```  

3. Ssh to the server and paste the public key to server on `~/.ssh/authorized_keys` file.  
Create `.ssh` directory if it doesn't exist on home directory, and change `.ssh` directory permission.
`.ssh` and `authorized_keys` must be owned by the user.  

```sh
mkdir -p ~/.ssh && chmod 700 ~/.ssh
```  

Paste the key and change `authorized_keys` file permission.
```sh
chmod 600 ~/.ssh/authorized_keys
```  


4. Single command
```sh
cat /path/to/ecdsa_public_key_openssh.pub | ssh user@server_ip "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
```  


## SSH configurations and hardening  
1. Ensure the server allows ECDSA public key authentication. Check the `/etc/ssh/sshd_config` file on the server:
```sh
sudo vim /etc/ssh/sshd_config
```  

Ensure this line is available to allow login using publickey.
```conf
PubkeyAuthentication yes
```  

2. Complete `sshd_config` with ssh hardening.  
```conf
Include /etc/ssh/sshd_config.d/*.conf
KbdInteractiveAuthentication no
UsePAM yes
PrintMotd no
AcceptEnv LANG LC_*
Subsystem       sftp    /usr/lib/openssh/sftp-server
ChallengeResponseAuthentication no

PubkeyAuthentication yes         # Allow public key authentication
PasswordAuthentication no        # Disable password-based authentication
PermitRootLogin no               # Disable root login
PermitEmptyPasswords no          # Prevent empty password logins
X11Forwarding no                 # Disable X11 forwarding unless explicitly needed
AllowTcpForwarding no            # Disable TCP forwarding unless explicitly needed
MaxAuthTries 3                   # Limit the number of failed authentication attempts
PermitUserEnvironment no         # Disable reading user environment files
ClientAliveInterval 300          # Disconnect idle clients after 5 minutes
ClientAliveCountMax 0            # Terminate connections after the first interval
AllowUsers youruser              # Allow specific users
LogLevel VERBOSE                 # Increase logging for security auditing
Banner /etc/issue.net            # Display a custom banner (optional, for legal notices)
```


## Reference
1. [ECDSA wiki](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm)  
2. [ECDSA encryptionconsulting](https://www.encryptionconsulting.com/education-center/what-is-ecdsa/)  
3. [Encryption Algorithm](https://www.encryptionconsulting.com/education-center/what-is-an-encryption-algorithm/)  
4. [OpenSSH](https://www.openssh.com/)
5. [SSH wiki](https://en.wikipedia.org/wiki/Secure_Shell)
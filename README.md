# SSH
Create SSH keys using `ECDSA` algorithm.
- Elliptic Curve Digital Signature Algorithm (ECDSA) is a Digital Signature Algorithm (DSA) which uses keys derived from elliptic curve cryptography (ECC).
- ECDSA is one of the more complex public key cryptography encryption algorithms.

## Generate keys  

1. **Public key**  
```sh
openssl ecparam -genkey -name prime256v1 -noout -out /path/to/ecdsa_private_key_name.pem
```

2. **Public key**  
```sh
openssl ec -in /path/to/ecdsa_private_key_name.pem -pubout -out /path/to/ecdsa_public_key_name.pem
```


3. **Convert Public Key to OpenSSH format**  
Servers requires public key in `OpenSSH` format for authentication.  
```sh
ssh-keygen -f /path/to/ecdsa_public_key_name.pem -i -m PKCS8 > /path/to/public_key_openssh.pub
```

## Copy Public key to the server
1. Copy public key using `OpenSSH` format.  
```sh
cat /path/to/ecdsa_public_key_openssh.pub
```  

2. Ssh to the server and paste the public key to server on `~/.ssh/authorized_keys` file.  
Create `.ssh` directory if it doesn't exist on home directory, and change `.ssh` directory permission.
```sh
mkdir -p ~/.ssh && chmod 700 ~/.ssh
```  

Paste the key and change `authorized_keys` file permission.
```sh
chmod 600 ~/.ssh/authorized_keys
```  


**Single command**
```sh
cat /path/to/ecdsa_public_key_openssh.pub | ssh user@server_ip "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
```  

## Reference
1. [ECDSA wiki](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm)  

2. [ECDSA encryptionconsulting](https://www.encryptionconsulting.com/education-center/what-is-ecdsa/)  

3. [Encryption Algorithm](https://www.encryptionconsulting.com/education-center/what-is-an-encryption-algorithm/)  
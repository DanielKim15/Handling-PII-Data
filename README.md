# Handling-PII-Data 
When encountering a bureaucracy issue with the client that prevents you from receiving the data early, here are a few ways that could help expedite the process:
<br />
1. Encrypting the data <br />
2. Creating Synthetic Data

<br />
For this respository we will use 

<br />

## Real life Scenario/Why is this important? <br />
A few months ago I encountered a problem to where I cannot recieve the data in time because the client had forgot to tell their lawyers about the project. Due to the Personal Identifiable Information (PII) within the data the lawyers had to go through the entire process of getting it approved, and then go through the process of eliminating some of the PII data before they can send it to us. Considering how we had limited time with the project, We had to find a way to speed up the process by providing options to enhance data security. One of the methods we used to help enhance the security and help speed up the process was encrypting the data.

<br />

## Part 1: Encrypting the Data

Now the goal of this encryption in python is to add another layer of security to files after you chose the encrypted file option from Windows/Mac files.
<br />
Here are the libraries you need to get started with the encryption:



1. **Importing Libraries:**
   - `getpass` for securely entering a password.
   - `argon2` for hashing the password.
   - `Crypto.Cipher` and `Crypto.Util.Padding` from the `pycryptodome` package for encryption and padding.
   - `os` for generating a random Initialization Vector (IV).
   - `hashlib` for hashing operations.
   - getpass for securely entering a password without echoing it to the console.

<br />

There’s two concepts to note when using the encryption: Hashing and AES encryption. Hashing turns the values from a password to randomized strings and special characters to ensure randomness. So when a hacker tries to brute force their way to guessing the password with an algorithm, the time it will take will be so slow that their computer might take years to finally break through. Since Argon2 was the winner in the 2015 Password Hashing Competition we will be use this library for it. For the AES encryption, it uses a 256 bit key to convert the password or data into a cipher. Considering how extremely rare it is for someone to brute force their way into the encryption, adding this alongside the hashed password will further increase security for your data.

2. **Password Input:**
   - Securely input a user password without echoing it on the console using getpass.
   -```python
   password = getpass.getpass() 

3. **Password Hashing with Argon2:**
   - Use Argon2, a secure password hashing algorithm, to hash the input password.
   - ```python
   hasher = argon2.PasswordHasher()
   hashed_password = hasher.hash(password)
   ```python

4. **Initialization Vector (IV) Generation:**
   - Generate a random Initialization Vector (IV) for AES encryption. This adds randomness to the encryption process.

5. **Deriving the AES Key:**
   - Derive an AES encryption key from the hashed password using SHA-256 hashing.

6. **Reading and Padding the File:**
   - Read the file to be encrypted and pad it to align with AES's block size requirement.

7. **File Encryption:**
   - Encrypt the file using AES in CBC mode with the derived key and IV.

8. **Saving the Encrypted File:**
   - Save the IV and the encrypted data to a new file. The IV is essential for decryption.

Encryption Process Explanation
The script follows these steps:

1. Importing Libraries
Various Python libraries are used for the encryption process:

python
Copy code
import getpass
import argon2
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad
import os
import hashlib
2. Secure Password Input
The password is securely input using getpass.getpass(), which hides the password from the console:

python
Copy code
password = getpass.getpass()
3. Password Hashing with Argon2
The password is hashed using the secure Argon2 algorithm:

python
Copy code
hasher = argon2.PasswordHasher()
hashed_password = hasher.hash(password)
4. IV Generation
A random Initialization Vector (IV) for AES encryption is generated:

python
Copy code
iv = os.urandom(16)
5. AES Key Derivation
An AES encryption key is derived from the hashed password using SHA-256:

python
Copy code
aes_key = hashlib.sha256(hashed_password.encode()).digest()
6. Reading and Padding the File
The file is read and padded to align with AES's block size requirement:

python
Copy code
with open("practice.ipynb", "rb") as f:
    file_data = f.read()
padded_data = pad(file_data, AES.block_size)
7. File Encryption
The file is encrypted using AES in CBC mode:

python
Copy code
cipher = AES.new(aes_key, AES.MODE_CBC, iv)
encrypted_data = cipher.encrypt(padded_data)
8. Saving the Encrypted Data
The IV and the encrypted data are saved to a new file:

python
Copy code
with open("encrypted_practice.ipynb", "wb") as f:
    f.write(iv)
    f.write(encrypted_data)
Notes
The script requires the presence of practice.ipynb in the working directory.
The output file encrypted_practice.ipynb contains the encrypted data.
Decryption requires the same password, IV, and key derivation process.


<br />

Once we presented the clients these options, we were able to recieve the data faster than if we had just waited for their beurocracy to finish. However considerable amount of time has already passed before we obtained the data, and it greatly impacted the quality of our analysis. Another method that I recently used that could be used to speed the beaurocracy even further, or possibly avoid it all, is creating syntehtic data from their original data.

<br />
## Part 2: Creating Synthetic Data from the real life data


# Streamlining Data Access: A Guide to Data Encryption with Python
When encountering a bureaucracy issue with the client that prevents you from receiving the data early, here are a few ways that could help expedite the process:
<br />
1. Encrypting the data <br />
2. Creating Synthetic Data

<br />
For this respository we will be focusing on the Encryption aspect of the data file.

<br />

## Real life Scenario/Why is this important? <br />

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

There’s two concepts to note when using encryption: Hashing and AES encryption. Hashing turns the values from a password to randomized strings and special characters to ensure randomness. So when a hacker tries to brute force their way to guessing the password with an algorithm, the time it will take will be so slow that their computer might take years to finally break through. Since Argon2 was the winner in the 2015 Password Hashing Competition we will be use this library for it. For the AES encryption, it uses a 256 bit key to convert the password or data into a cipher. Considering how extremely rare it is for someone to brute force their way into the encryption, adding this alongside the hashed password will further increase security for your data.

2. **Password Input:**
   - Securely input a user password without echoing it on the console using getpass.
``` python
 password = getpass.getpass()

```

3. **Password Hashing with Argon2:**
   - Use Argon2, a secure password hashing algorithm, to hash the input password.
``` python
   hasher = argon2.PasswordHasher()
   hashed_password = hasher.hash(password)
   
```

4. **Initialization Vector (IV) Generation:**
   - Generate a random Initialization Vector (IV) for AES encryption. This adds randomness to the encryption process.
``` python
iv = os.urandom(16)   
```
   
5. **Deriving the AES Key:**
   - Derive an AES encryption key from the hashed password using SHA-256 hashing.
``` python
aes_key = hashlib.sha256(hashed_password.encode()).digest()

```   
   

6. **Reading and Padding the File:**
   - Read the file to be encrypted and pad it to align with AES's block size requirement.

``` python
with open("practice.ipynb", "rb") as f:
    file_data = f.read()
padded_data = pad(file_data, AES.block_size)

```

7. **File Encryption:**
   - Encrypt the file using AES in CBC mode with the derived key and IV.
``` python
cipher = AES.new(aes_key, AES.MODE_CBC, iv)
encrypted_data = cipher.encrypt(padded_data)

```

8. **Saving the Encrypted File:**
   - Save the IV and the encrypted data to a new file. The IV is essential for decryption.
``` python

with open("encrypted_practice.ipynb", "wb") as f:
    f.write(iv)
    f.write(encrypted_data)

```

<br />


## Part 2: Storing your password

<br />

Now that you've built the encrypted file you want to be able hide your keys away from everyone else. Now you can write down the password on a piece of paper, but that takes precious time that can be used on something else. Luckily there is a way where we can store the password within the computer in a safeplace using a package called keyring.

<br />

Here are the steps needed to proceed with password saving:

1. **Importing Libraries and Modules:**
   - keyring for accessing and manipulating the system's keyring service.
   - keyring.util.platform_ for accessing platform-specific keyring configuration.

``` python
import keyring
import keyring.util.platform_ as keyring_platform

```

<br />


2. **Password Input:**
   - Securely input a user password without echoing it on the console using getpass.
``` python
 password = getpass.getpass()

```

3. **Printing Configuration and Data Root:**
   - The script prints the configuration and data root directories for the keyring service on your system. This can vary depending on your operating system       and environment.
``` python
print(keyring_platform.config_root())
# Output might be: /home/username/.config/python_keyring

   
```

4. **Displaying the Active Keyring Backend:**
   - The script prints the current backend being used by the keyring library. This shows which service is being used to store and manage the passwords.
``` python
print(keyring.get_keyring())
# Output might be: keyring.backends.SecretService.Keyring (priority: 5)

```
   
5. **Setting Namespace and Entry:**
   - Defines a namespace and an entry for storing the password. This helps in organizing and retrieving the credentials easily.
``` python
NAMESPACE = "my-app"
ENTRY = "API_KEY"

```   
   

6. **Storing the Password:**
   - Retrieves and prints the password associated with the specified namespace and entry.

``` python
keyring.set_password(NAMESPACE, ENTRY, hashed_password)

```

7. **Fetching Credentials as an Object:**
   - Retrieves the credentials as a Credential object, which includes both the username (entry) and the password. This is useful for scenarios where you need both pieces of information.
``` python
cred = keyring.get_credential(NAMESPACE, ENTRY)
print(f"Password for username {cred.username} in namespace {NAMESPACE} is {cred.password}")
# Output: Password for username API_KEY in namespace my-app is [stored password]


```

<br />
Something to note: hashed_password is a variable where the password is stored from part 1. In case you've run the coding in another file and your running this file separately, be sure to replace the variable with the actual password of yours. 
Now that everything is set up, lets test out the decryption of the file!

<br />

## Part 3: De-encrypting the Data

The data is encrypted, the key is stashed, now lets give it a shot and see if we can access our files!
 <br />

1. **Importing Libraries and Modules:**
   - keyring for retrieving the stored password.
   - argon2 is not directly used in this script but generally used for hashing passwords.
   - Crypto.Cipher.AES and Crypto.Util.Padding.unpad from the pycryptodome package for decryption and removing padding.
   - os and hashlib for handling file operations and hashing.

``` python
import keyring
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad
import os
import hashlib

```

<br />


2. **Retrieving the Stored Password:**
   - The script retrieves the stored password from the keyring using the specified namespace and entry.
``` python
 password = keyring.get_password("my-app", "API_KEY")

```

3. **Reading the Encrypted File:**
   - Opens the encrypted file and reads the Initialization Vector (IV) and the encrypted data. The IV is typically stored at the beginning of the file.
``` python
with open("C:/Users/CENSEO/Downloads/encrypted_practice.ipynb", "rb") as f:
    iv = f.read(16)  # Read the 16-byte IV
    encrypted_data = f.read()  # Read the remaining encrypted data

   
```

4. **Deriving the AES Key:**
   - Derives the AES encryption key from the password. Here, a simple SHA-256 hash of the password is used to generate a 256-bit key.
``` python
aes_key = hashlib.sha256(password.encode()).digest()


```
   
5. **Initializing the AES Cipher for Decryption:**
   - Sets up the AES cipher with the derived key and the IV for decryption.
``` python
cipher = AES.new(aes_key, AES.MODE_CBC, iv)


```   
   

6. **Decrypting the Data:**
   - Decrypts the encrypted data and then removes the padding to get the original file content.

``` python
decrypted_data = unpad(cipher.decrypt(encrypted_data), AES.block_size)


```

7. **Writing the Decrypted Data to a File:**
   - Writes the decrypted data to a new file, effectively restoring the original content.
``` python
with open("C:/Users/dkim/Downloads/dencrypted_practice_2.ipynb", "wb") as f:
    f.write(decrypted_data)


```

<br />
And there you have it: You're able to gain access through your de-encrypted data and now you can play around with it.

<br />

## Closing Thoughts:

<br />

Keep in mind the encryption from the this coding is very simple compared to other paid softwares out there. I wanted to provide a quick overview to get started on encrypting your data file using free resources in case you have a need for it. In the next part I plan to go over another method where you can start a surface level analysis without the need of real data: Synthetic Data.




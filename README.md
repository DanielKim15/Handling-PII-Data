# Handling-PII-Data Part 1: Encrypting the Data
When encountering a bureaucracy issue with the client that prevents you from receiving the data early, here are a few ways that could help expedite the process:
<br />-
1. Encrypting the data <br />
2. Creating Synthetic Data

<br />
## Encryption Process Explanation

This script demonstrates the process of encrypting a file using the AES (Advanced Encryption Standard) cipher in Cipher Block Chaining (CBC) mode. Below is a step-by-step explanation of the code:

1. **Importing Libraries:**
   - `getpass` for securely entering a password.
   - `argon2` for hashing the password.
   - `Crypto.Cipher` and `Crypto.Util.Padding` from the `pycryptodome` package for encryption and padding.
   - `os` for generating a random Initialization Vector (IV).
   - `hashlib` for hashing operations.

2. **Password Input:**
   - Securely obtain a password from the user.

3. **Password Hashing with Argon2:**
   - Hash the password using Argon2.

4. **Initialization Vector (IV) Generation:**
   - Generate a 16-byte random IV.

5. **Deriving the AES Key:**
   - Hash the Argon2 hashed password and use it as the AES encryption key.

6. **Reading the File to Encrypt:**
   - Open and read the contents of the file to be encrypted.

7. **Padding the File Data:**
   - Pad the data to be a multiple of AES's block size (16 bytes).

8. **Creating an AES Cipher:**
   - Create an AES cipher object with the derived key, CBC mode, and the IV.

9. **Encrypting the Data:**
   - Encrypt the padded file data.

10. **Writing IV and Encrypted Data:**
    - Write the IV and the encrypted data to a new file.

### Code

```python
import getpass
import argon2
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad
import os
import hashlib

# Get the password from the user
password = getpass.getpass()

# Hash the password with Argon2
hasher = argon2.PasswordHasher()
hashed_password = hasher.hash(password)

# Generate a random initialization vector (IV)
iv = os.urandom(16)

# Derive the AES key from the password
aes_key = hashlib.sha256(hashed_password.encode()).digest()

# Open the file to encrypt
with open("practice.ipynb", "rb") as f:
    file_data = f.read()

# Pad the file data
padded_data = pad(file_data, AES.block_size)

# Create an AES cipher
cipher = AES.new(aes_key, AES.MODE_CBC, iv)

# Encrypt the data
encrypted_data = cipher.encrypt(padded_data)

# Write the IV and encrypted data to a file
with open("encrypted_practice.ipynb", "wb") as f:
    f.write(iv)
    f.write(encrypted_data)


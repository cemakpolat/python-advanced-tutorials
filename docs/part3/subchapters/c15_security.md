---
title: Security Considerations in Python
parent: Applying Advanced Concepts 
nav_order: 5
---

## Chapter 15: Security Considerations in Python

*   **15.1 Introduction to Security in Python:**

    Security should be a primary concern throughout the software development lifecycle, not an afterthought. Python, while generally considered a safe language, is not immune to security vulnerabilities. A proactive approach to security is essential for protecting your applications and data from malicious actors.

    Why security matters in Python development:

    *   **Data Sensitivity:** Many Python applications handle sensitive data, such as user credentials, financial information, and personal data. Protecting this data is crucial for maintaining user privacy and complying with regulations.
    *   **Web Application Vulnerabilities:** Python is commonly used for building web applications, which are often exposed to the public internet and are therefore vulnerable to a wide range of attacks.
    *   **Code Injection:** Python's dynamic nature can make it susceptible to code injection attacks if user input is not properly validated.
    *   **Supply Chain Risks:** As applications grow, they import more and more dependencies which might have a backdoor which can be used to attack the system
    *   **Cloud Deployment:** With the rise of cloud computing, Python applications are often deployed in environments that are shared with other users and applications. This increases the risk of security breaches.

    Key security principles:

    *   **Principle of Least Privilege:** Grant users and processes only the minimum level of access that they need to perform their tasks. This limits the damage that can be done if an account or process is compromised.
    *   **Defense in Depth:** Implement multiple layers of security controls. If one layer fails, other layers will still provide protection.

    Common security threats to Python applications:

    *   **Web Application Vulnerabilities:** Cross-Site Scripting (XSS), Cross-Site Request Forgery (CSRF), SQL injection, and other common web application vulnerabilities.
    *   **Data Breaches:** Unauthorized access to sensitive data.
    *   **Code Injection:** Exploiting vulnerabilities to inject and execute malicious code.
    *   **Denial-of-Service Attacks:** Overwhelming a system with traffic to make it unavailable to legitimate users.
    *   **Dependency Vulnerabilities:** Malicious code in your dependendies
    *   **Confidentiality Break:** Disclosing PII
    *   **Data validation break**
    *   **Broken Access Control**

    **Ethical and Legal Implications of Security Vulnerabilities:**

    It is *crucial* to understand the ethical and legal implications of security vulnerabilities. Exploiting security vulnerabilities without authorization is illegal and unethical. You should only use your knowledge of security vulnerabilities for legitimate purposes, such as:

    *   Ethical hacking (with proper authorization)
    *   Penetration testing
    *   Security research
    *   Developing defensive tools
    *   Responsible disclosure of vulnerabilities to vendors

*   **15.2 Input Validation and Sanitization:**

    Input validation and sanitization are essential for preventing many common security vulnerabilities. By carefully validating and sanitizing user input, you can prevent attackers from injecting malicious code, accessing unauthorized data, or causing other harm.

    Why input validation is critical:

    *   **User input is untrusted:** You should never assume that user input is safe or well-formatted.
    *   **Attackers can exploit vulnerabilities:** Attackers can exploit vulnerabilities in your code to inject malicious code or access sensitive data.
    *   **Input validation is the first line of defense:** By validating input, you can prevent many common attacks before they even reach your application logic.

    **Validating User Input:**

    *   **Data Types:** Ensure that the input data is of the expected type (e.g., integer, string, email address).
    *   **Ranges:** Ensure that numerical values are within the expected range (e.g., age between 0 and 120).
    *   **Formats:** Ensure that strings match the expected format (e.g., email address, phone number, date).

    ```python
    def validate_age(age):
        """Validates that the age is a valid integer within a reasonable range."""
        try:
            age = int(age)
            if not 0 <= age <= 120:
                raise ValueError("Age must be between 0 and 120")
            return age
        except ValueError:
            raise ValueError("Invalid age format. Must be an integer.")
    ```

    **Sanitizing User Input:**

    Sanitizing user input involves removing or escaping potentially harmful characters. This is particularly important for preventing code injection and XSS vulnerabilities.

    *   **HTML Encoding:** Escape HTML characters to prevent XSS attacks. You can use the `html` module for this.
    ```python
    import html

    def sanitize_html(text):
        """Escapes HTML characters to prevent XSS."""
        return html.escape(text)
    ```

    *   **URL Encoding:** Escape URL characters to prevent URL injection attacks. You can use the `urllib.parse` module for this.

    *   **SQL Escaping:** This is not recommended as the preferred way is using parameterized queries. Use parameterized queries with your database library to prevent SQL injection attacks.

    **Using Regular Expressions for Input Validation:**

    Regular expressions can be used to validate complex input formats, such as email addresses, phone numbers, and URLs.

    ```python
    import re

    def validate_email(email):
        """Validates that the email address is in a valid format."""
        pattern = r"^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$"
        if re.match(pattern, email):
            return email
        else:
            raise ValueError("Invalid email address format")
    ```

    **Preventing Command Injection, SQL Injection, and Cross-Site Scripting (XSS) Vulnerabilities:**

    *   **Command Injection:** To prevent command injection vulnerabilities, *never* use user input directly in shell commands. Use the `subprocess` module with the `shell=False` option and pass the command and arguments as a list.

    *   **SQL Injection:** To prevent SQL injection vulnerabilities, *always* use parameterized queries with your database library or use an ORM (Object-Relational Mapper). Never construct SQL queries by concatenating user input.

    *   **Cross-Site Scripting (XSS):** To prevent XSS vulnerabilities, always escape HTML characters in user-generated content before displaying it in your web application.

    **Handling User Uploaded Content:**
    Never trust that a filename sent by an attacker is the filename.
        *   **Validating File Types and Sizes:**

            *   Check the file extension. Use a whitelist of allowed extensions, rather than a blacklist of disallowed extensions.
            *   Check the file size to prevent denial-of-service attacks.
            *   Check the file content using libraries like `libmagic` to verify that the file type matches the extension.
        *   **Storing Files Securely:**
        *   **Proper disposal of resources after usage to prevent information leakage**
    *   Use a dedicated storage service (e.g., Amazon S3, Google Cloud Storage) to store user-uploaded files.
        *   Generate unique filenames for each file to prevent filename collisions.
        *   Store files outside of the web server's document root to prevent direct access.

*   **15.3 Authentication and Authorization (Revisited with Security Focus):**

    Secure authentication and authorization are essential for protecting your application from unauthorized access.

    **Secure Password Storage:**

    *   *Never* store passwords in plain text.
    *   Use a strong key derivation function (e.g., Argon2, scrypt) to derive the encryption key from a user-provided master password.

        Store passwords using a strong hashing algorithm (e.g., bcrypt, scrypt, Argon2) with a unique salt for each password.

        ```python
        import bcrypt

        def hash_password(password):
            """Hashes a password using bcrypt."""
            salt = bcrypt.gensalt()
            hashed_password = bcrypt.hashpw(password.encode('utf-8'), salt)
            return hashed_password

        def verify_password(password, hashed_password):
            """Verifies a password against a hashed password."""
            return bcrypt.checkpw(password.encode('utf-8'), hashed_password)
        ```

    *   **Key Stretching:**

        Use key stretching to make it more difficult for attackers to crack passwords using brute-force attacks. Key stretching involves repeatedly hashing the password to increase the computational cost of cracking the password.

    **Using Strong Authentication Protocols:**

    *   **OAuth 2.0:** A standard protocol for authorization that allows users to grant third-party applications access to their resources without sharing their credentials.
    *   **JWT (JSON Web Tokens):** A standard for creating access tokens that contain claims about the user. JWTs can be used for both authentication and authorization.

    **Implementing Authorization:**

    *   **Role-Based Access Control (RBAC):** Assign users to roles, and grant permissions to roles.
    *   **Attribute-Based Access Control (ABAC):** Grant permissions based on attributes of the user, the resource, and the environment.

    **Preventing Common Authentication Vulnerabilities:**

    *   **Brute-Force Attacks:** Implement rate limiting to prevent attackers from trying to guess passwords repeatedly.
    *   **Session Hijacking:** Use secure cookies (with the `HttpOnly` and `Secure` flags) and session timeouts to prevent session hijacking.
    *   **Insecure Tokens:** Do not implement tokens by yourself, use external libraries

    **Implementing API Keys Authentication and Authorization:**

        Implementing API keys is a common practice to allow different services to talk each other.
        ```python
        from fastapi import FastAPI, Depends, HTTPException, Header
        from typing import Optional

        app = FastAPI()

        #Store API KEYS, change this to a production ready DB or secure storage system
        api_keys = {
            "secret-key-1": "jhon",
            "secret-key-2": "alice",
        }

        async def verify_api_key(x_api_key: Optional[str] = Header(None)):
            if not x_api_key:
                 raise HTTPException(status_code=400, detail="x_api_key header is required")

            if x_api_key not in api_keys:
                raise HTTPException(status_code=401, detail="Invalid API Key")
            return api_keys[x_api_key] #Return the user logged

        @app.get("/items/")
        async def read_items(username: str = Depends(verify_api_key)):
            return {"message": f"Hello {username}!"}
        ```

*   **15.4 Secure File Handling:**

    *   Preventing Path Traversal Vulnerabilities:
        *   Always validate and sanitize user-provided filenames before using them to access files.
        *   Use `os.path.abspath()` to normalize the path and remove any relative path components (e.g., `..`).
        *   Check that the normalized path is within the expected directory.

        ```python
        import os

        def secure_filepath(basepath, filename):
            """Secures a filepath against path traversal attacks."""
            filepath = os.path.join(basepath, filename)
            filepath = os.path.abspath(filepath)
            if not filepath.startswith(basepath):
                raise ValueError("Invalid filepath: Path traversal detected")
            return filepath
        ```

    *   Validating File Types and Sizes:

        *   Check the file extension. Use a whitelist of allowed extensions, rather than a blacklist of disallowed extensions.
        *   Check the file size to prevent denial-of-service attacks.
        *   Check the file content using libraries like `libmagic` to verify that the file type matches the extension.

    *   Storing Files Securely:

        *   Use a dedicated storage service (e.g., Amazon S3, Google Cloud Storage) to store user-uploaded files.
        *   Generate unique filenames for each file to prevent filename collisions.
        *   Store files outside of the web server's document root to prevent direct access.
        *   Set appropriate permissions on the files and directories to prevent unauthorized access.
* Protecting user privacy by removing PII

*   Proper disposal of resources after usage to prevent information leakage


*   **15.5 Cryptography in Python:**

    The `cryptography` library provides a high-level interface to cryptographic primitives.

    *   **Symmetric Encryption:**

        Symmetric encryption algorithms use the same key for encryption and decryption. Common symmetric encryption algorithms include AES and ChaCha20.

        ```python
        from cryptography.fernet import Fernet

        # Generate a key (store it securely!)
        key = Fernet.generate_key()
        cipher = Fernet(key)

        # Encrypt the data
        data = b"My secret message"
        encrypted_data = cipher.encrypt(data)

        # Decrypt the data
        decrypted_data = cipher.decrypt(encrypted_data)
        ```

    *   **Asymmetric Encryption:**

        Asymmetric encryption algorithms use a pair of keys: a public key and a private key. The public key can be shared with anyone, while the private key must be kept secret. Data encrypted with the public key can only be decrypted with the corresponding private key, and vice versa. Common asymmetric encryption algorithms include RSA and ECDSA.

        ```python
        from cryptography.hazmat.primitives.asymmetric import rsa
        from cryptography.hazmat.primitives import hashes
        from cryptography.hazmat.primitives.asymmetric import padding
        from cryptography.hazmat.backends import default_backend
        from cryptography.hazmat.primitives import serialization

        # Generate a private key
        private_key = rsa.generate_private_key(
            public_exponent=65537,
            key_size=2048,
            backend=default_backend()
        )

        # Get the public key
        public_key = private_key.public_key()

        # Serialize the public key for sharing (PEM format)
        pem = public_key.public_bytes(
           encoding=serialization.Encoding.PEM,
           format=serialization.PublicFormat.SubjectPublicKeyInfo
        )

        # Serialize the private key for local use, encrypting it for security.
        private_pem = private_key.private_bytes(
            encoding=serialization.Encoding.PEM,
            format=serialization.PrivateFormat.PKCS8,
            encryption_algorithm=serialization.BestAvailableEncryption(b"your_password")
        )

        # Encryption and Decryption:
        # Important: In reality you should not encrypt big documents
        # and you must take to account the proper padding on your use case
        # This code is just for educational purpose

        # Encrypt the data using the public key
        data = b"My secret message"
        encrypted = public_key.encrypt(
            data,
            padding.OAEP(
                mgf=padding.MGF1(algorithm=hashes.SHA256()),
                algorithm=hashes.SHA256(),
                label=None
            )
        )

        # Load the private key to decrypt the message.
        loaded_private_key = serialization.load_pem_private_key(
           private_pem,
           password=b"your_password",
           backend=default_backend()
        )

        # Decrypt the data using the private key
        original_message = loaded_private_key.decrypt(
            encrypted,
            padding.OAEP(
                mgf=padding.MGF1(algorithm=hashes.SHA256()),
                algorithm=hashes.SHA256(),
                label=None
            )
        )

        print('OK!')
        ```

    *   **Hashing:**

        Hashing algorithms are used to create a one-way hash of data. The hash is a fixed-size string of characters that is unique to the input data. Hashing algorithms are used for password storage, data integrity checks, and other security applications.

        ```python
        import hashlib

        # Hash the data using SHA-256
        data = b"My secret message"
        hash_object = hashlib.sha256(data)
        hex_digest = hash_object.hexdigest() # Returns hexadecimal representation of object.
        print(hex_digest)
        ```

    *   **Digital Signatures:**

        Digital signatures are used to verify the integrity and authenticity of data. A digital signature is created by signing the data with the sender's private key. The signature can then be verified by anyone who has the sender's public key.

        ```python
        from cryptography.hazmat.primitives import hashes
        from cryptography.hazmat.primitives.asymmetric import padding
        from cryptography.hazmat.backends import default_backend
        from cryptography.hazmat.primitives import serialization

        # Create keys like showed before
        # Sign the data with the private key
        message = b"My important document"
        signature = private_key.sign(
            message,
            padding.PSS(
                mgf=padding.MGF1(hashes.SHA256()),
                salt_length=padding.PSS.MAX_LENGTH
            ),
            hashes.SHA256()
        )

        # Verify the signature with the public key
        try:
            public_key.verify(
                signature,
                message,
                padding.PSS(
                    mgf=padding.MGF1(hashes.SHA256()),
                    salt_length=padding.PSS.MAX_LENGTH
                ),
                hashes.SHA256()
            )
            print("Signature is valid")
        except Exception:
            print("Signature is invalid")
        ```

*   **15.6 Web Application Security:**

    Web applications are a common target for attackers, and it's essential to understand common web application vulnerabilities and how to prevent them.

    *   **Cross-Site Scripting (XSS):**

        XSS attacks involve injecting malicious JavaScript code into a web page, which is then executed by other users who visit the page. To prevent XSS attacks:

        *   **Encode output:** Always encode user-generated content before displaying it in your web application. Use appropriate encoding functions for the output context (e.g., HTML encoding for HTML content, JavaScript encoding for JavaScript content).
        *   **Validate input:** Validate user input to ensure that it does not contain any malicious code.
        *   **Content Security Policy (CSP):** CSP is a HTTP header that allows you to specify the sources from which the browser is allowed to load resources. This can help to prevent XSS attacks by limiting the attacker's ability to inject malicious code.

    *   **Cross-Site Request Forgery (CSRF):**

        CSRF attacks involve tricking a user into performing an action on a web application without their knowledge or consent. To prevent CSRF attacks:

        *   **Use CSRF tokens:** Generate a unique, unpredictable token for each user session and include the token in every form and AJAX request. Verify the token on the server-side to ensure that the request is legitimate.
        *   **SameSite cookies:** Configure your cookies with the `SameSite` attribute to prevent them from being sent with cross-site requests.

    *   **SQL Injection:**

        SQL injection attacks involve injecting malicious SQL code into a database query. To prevent SQL injection attacks:

        *   **Use parameterized queries:** Always use parameterized queries with your database library or use an ORM (Object-Relational Mapper). Never construct SQL queries by concatenating user input.
        *   **Principle of Least Privilege:** If is really important for your application to prevent SQL injection, it is recommended to create an account that has readonly permissions.

    *   **Session Management:**

        *   Use secure cookies (with the `HttpOnly` and `Secure` flags) and session timeouts to prevent session hijacking.
            *   `HttpOnly`: Cookies with the `HttpOnly` flag cannot be accessed by JavaScript code, which reduces the risk of XSS attacks.
            *   `Secure`: Cookies with the `Secure` flag are only transmitted over HTTPS, which protects them from eavesdropping.
        * Implement best practice for session management.

    *   **Other Common Web Application Vulnerabilities:**

        *   **Clickjacking:** An attack that tricks users into clicking on something different from what they perceive, potentially leading to unintended actions.
        *   **Buffer overflows:** These occur when a program writes data beyond the allocated buffer.
        *   **Format string bugs:** A vulnerability that occurs when a program uses user-supplied input as a format string in a function like printf.
        *   **Server Side Request Forgery (SSRF):** This is a web security flaw that enables an attacker to force the server-side application to make requests to an unintended location

*   **15.7 Security Best Practices for Dependencies:**

    Managing dependencies securely is crucial for maintaining the overall security of your Python applications. Vulnerable dependencies can introduce security risks, even if your own code is secure.
        *   **Always keep the version the same as you want, with pinning:**
        ```
        requests==2.25.1
        ```

    *   **Dependency Management with `pip` and Virtual Environments:**

        *   Use virtual environments to isolate your project's dependencies from the system-level Python installation. This prevents conflicts between dependencies and ensures that your application has a consistent environment.

            ```bash
            python3 -m venv .venv  # Create a virtual environment
            source .venv/bin/activate  # Activate the virtual environment
            pip install -r requirements.txt  # Install dependencies
            ```

    *   **Using a Requirements File to Track Dependencies:**

        *   Create a `requirements.txt` file to list all of your project's dependencies and their versions. This makes it easy to reproduce the environment and ensures that everyone on the team is using the same versions of the dependencies.

            ```bash
            pip freeze > requirements.txt  # Generate the requirements file
            pip install -r requirements.txt  # Install dependencies from the requirements file
            ```

    *   **Scanning Dependencies for Vulnerabilities:**

        *   Use tools like `Safety` to scan your dependencies for known vulnerabilities. `Safety` checks your dependencies against a database of known vulnerabilities and alerts you to any potential security risks.
            ```bash
            pip install safety
            safety check -r requirements.txt
            ```
        *   Dependabot (or dependabot-like) will also generate information about security problems

    *   **Keeping Dependencies Up-to-Date:**

        *   Regularly update your dependencies to patch security vulnerabilities and take advantage of new features and performance improvements.
            *   Consider using Dependabot to monitor third party dependencies

    *   **Using Signed Packages When Possible:**
        *   Only use packages from trusted sources, and verify the signatures of the packages before installing them. The current version of PIP does not make it
*   **Third party validation**




*   **Project 15.1: Secure Password Manager**

    *   **Description:** Develop a command-line password manager that securely stores and retrieves user passwords. The password manager should:
        *   Store passwords in an encrypted format using a strong encryption algorithm (e.g., AES).
        *   Use a strong key derivation function (e.g., Argon2, scrypt) to derive the encryption key from a user-provided master password.
        *   Implement strong authentication
        *   Encrypt password for users so the admins can't read them.
        Here is an example of a good architecture
        ```mermaid
        sequenceDiagram
            participant User
            participant PasswordManager as PM
            participant Storage
            User->>PM: requests password for website X
            PM->>User: prompts for master password
            User->>PM: enters master password
            PM->>KDF: Derives encryption key from master password
            PM->>Storage: retrieves encrypted password data
            Storage-->>PM: encrypted password data
            PM->>Encryption: decrypts password using derived key
            Encryption-->>PM: plaintext password
            PM->>User: displays password
        ```

        ```python
        import os
        import hashlib
        import base64
        from cryptography.fernet import Fernet
        from cryptography.hazmat.primitives import hashes
        from cryptography.hazmat.primitives.kdf.pbkdf2 import PBKDF2HMAC
        from cryptography.hazmat.backends import default_backend
        import getpass

        # Secure password storage
        class SecurePasswordManager:
            def __init__(self, master_password):
                # Key Derivation Function (KDF) for creating a strong encryption key from the master password
                # Create Salty
                salt = b'\xb4\xbaW\x9b\xfa\xb1\x93\xbb\xc4\x91V\xbb\xb4\x8a\x01\x08'
                kdf = PBKDF2HMAC(
                    algorithm=hashes.SHA256(),
                    length=32,
                    salt=salt,
                    iterations=100000,
                    backend=default_backend()
                )
                # Set the correct pass
                key = base64.urlsafe_b64encode(kdf.derive(master_password.encode())) #url safe encode is because it will be placed on URLs
                self.cipher = Fernet(key)

            def add_password(self, service, password):
                #Encrypt password to be added to the password storage
                encrypted_password = self.cipher.encrypt(password.encode())
                print('Password Encrypted')

            def get_password(self, service):
                #Get Password from the storage and decrypt it
                encrypt_password = 'password' #get
                self.cipher.decrypt(encrypt_password.encode()) #return password

        # Set the current pass
        MASTER_PASSWORD = getpass.getpass("Master password:") #Get password from CLI, you can store that in a file

        # Example usage:
        password_manager = SecurePasswordManager(MASTER_PASSWORD)
        password_manager.add_password("google", "mypass")
        password_manager.get_password("google")
        ```

*   **15.8 Conclusion:**

    This chapter provided a comprehensive overview of security considerations in Python. We explored input validation, authentication and authorization, secure file handling, cryptography, web application security, and security best practices for dependencies. By mastering these concepts, you'll be well-equipped to write more secure Python code.


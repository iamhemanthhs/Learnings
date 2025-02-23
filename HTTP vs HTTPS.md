# SSL/TLS Explained

![[Pasted image 20250222191042.png]]

## 1. Overview of SSL/TLS
SSL (Secure Sockets Layer) and TLS (Transport Layer Security) are cryptographic protocols used to secure communication over a network. TLS is the successor to SSL, offering enhanced security.

### Features:
- **Confidentiality**: Encrypts data to prevent unauthorized access.
- **Integrity**: Ensures data is not altered in transit.
- **Authentication**: Verifies the identity of the communicating parties.

## 2. SSL/TLS Handshake Process
The handshake establishes a secure connection between a **client** (e.g., browser) and a **server**.

### **Step 1: Client Hello**
- The client sends a `Client Hello` message containing:
  - Supported TLS versions (e.g., TLS 1.2, TLS 1.3)
	  - Supported cipher suites
  - A randomly generated `Client Random`

### **Step 2: Server Hello**
- The server responds with a `Server Hello` message containing:
  - Selected TLS version
  - Selected cipher suite
  - A randomly generated `Server Random`
  - The server’s digital certificate (includes its public key)

### **Step 3: Server Authentication & Key Exchange**
- The client verifies the server's certificate using a trusted **Certificate Authority (CA)**.
- The server may send a **key exchange message** depending on the cipher suite.

### **Step 4: Client Key Exchange**
- The client generates a **Pre-Master Secret**.
- It encrypts the pre-master secret using the **server’s public key** (from the certificate) and sends it to the server.
- The server **decrypts** this using its **private key**.

### **Step 5: Session Key Generation**
- Both client and server derive the same **session key** using:
  - **Pre-Master Secret**
  - **Client Random**
  - **Server Random**
  - **Key Derivation Function (KDF)** from the chosen cipher suite
- The session key is used for **symmetric encryption** of all communication.

### **Step 6: Client Finished**
- The client sends a message encrypted with the session key to confirm the handshake completion.

### **Step 7: Server Finished**
- The server sends its own encrypted "Finished" message.
- Secure communication begins using symmetric encryption.

## 3. Key Cryptographic Concepts
### **Asymmetric Encryption (Public-Key Cryptography)**
- Uses a key pair:
  - **Public Key** → Encrypts data
  - **Private Key** → Decrypts data
- Example: RSA, ECC

### **Symmetric Encryption**
- Uses a single key for both encryption and decryption.
- Faster than asymmetric encryption.
- Example: AES, ChaCha20

### **Digital Certificates & Authentication**
- Issued by **Certificate Authorities (CAs)**
- Contains:
  - Server’s public key
  - Identity details
  - CA’s signature (to verify authenticity)

## 4. Cipher Suites & Security
A **cipher suite** defines the encryption, authentication, and key exchange algorithms used.

### **Components of a Cipher Suite:**
- **Key Exchange**: RSA, ECDHE, Diffie-Hellman
- **Encryption Algorithm**: AES-GCM, ChaCha20
- **MAC (Message Authentication Code)**: HMAC-SHA256
- **PRF (Pseudo-Random Function)**: Used for key derivation (TLS 1.2)

### **Session Key Derivation Formula:**
```
Session Key = KDF(Pre-Master Secret, Client Random, Server Random)
```
- Both client and server **derive the exact same session key** independently.

## 5. TLS Versions & Security Improvements
| Version  | Status  | Key Features  |
|----------|---------|--------------|
| SSL 3.0  | Deprecated  | Vulnerabilities found |
| TLS 1.0  | Deprecated  | More secure than SSL |
| TLS 1.1  | Deprecated  | Improved cipher suites |
| TLS 1.2  | Widely used | Strong encryption & HMAC |
| TLS 1.3  | Latest & Secure | Faster handshake, no RSA key exchange |

## 6. Why is SSL/TLS Secure?
- **Public-key encryption ensures key secrecy** (only the server can decrypt the pre-master secret).
- **Session keys are never directly exchanged**; they are **derived** independently.
- **Even if handshake messages are intercepted**, an attacker cannot derive the session key without the **pre-master secret**.

## 7. Summary of Secure Communication Flow
1. Client and server exchange **hello messages**.
2. Server sends **certificate** (authentication).
3. Client encrypts **pre-master secret** with the server’s **public key**.
4. Server decrypts it with its **private key**.
5. Both generate **session keys** using the **cipher suite’s KDF**.
6. Secure **symmetric encryption** starts.

## 8. Key Takeaways
- **TLS is the secure version of SSL** (SSL is deprecated).
- **Handshake establishes trust** and sets up encryption.
- **Asymmetric encryption is used only during the handshake**, then **symmetric encryption is used for speed**.
- **TLS 1.3 is the latest and most secure version**.
# Scenario Description - Delegation: Parent Authorizing Guardian for School Pickup

A simple delegation use case: **a parent authorizing a guardian to pick up their child from school** using Decentralized Identifiers (DIDs) and Verifiable Credentials (VCs). This will highlight how DID and VC standards can simplify delegation in a secure and controlled way. In this scenario, a parent (Alice) needs to authorize a trusted guardian (Bob) to pick up her child from school.

---

## **Flow Breakdown**

### **Step 1: Issuance of Parent and Guardian DIDs**

1. **DID Creation**:
   - Alice and Bob each have their own DID, representing their unique, decentralized identity.
   - Alice’s DID might be created when she first registers as a parent with the school.
   - Bob’s DID can be issued similarly when he registers as a trusted contact or family friend with the school, or by Alice directly if the system allows for peer-issued DIDs.

### **Step 2: Parent Issues a Verifiable Credential to Delegate Authorization**

1. **Creating the Delegation Credential**:
   - Alice uses her DID-enabled digital wallet (or app) to issue a VC to Bob, authorizing him as a designated person for school pickup.
   - This VC includes information like:
     - Alice’s DID (the issuer of the credential)
     - Bob’s DID (the holder of the credential)
     - Authorization type (e.g., "Child Pickup Authorization")
     - Validity period (start and end dates for when Bob is authorized to pick up the child)
     - Optional: any restrictions, like pickup days or hours.

2. **Signing and Securing the Credential**:
   - Alice’s wallet signs this VC using her DID’s private key, ensuring authenticity.
   - The signed VC is securely sent to Bob’s digital wallet, allowing him to store the credential and present it when needed.

### **Step 3: Verification Process at the School**

1. **Presenting the Credential**:
   - When Bob arrives at the school for pickup, he presents the VC from his digital wallet to the school’s verification system.
   - This can be done through a QR code, NFC scan, or a mobile app that allows the school to scan and retrieve the credential data.

2. **School Verification**:
   - The school’s system checks:
     - **Credential Validity**: Ensures the VC is still within its authorized date range.
     - **Issuer Verification**: Validates that the VC was issued by Alice’s DID, confirming her authority as the parent.
     - **Signature Verification**: Uses cryptographic verification to confirm that the credential has not been tampered with.

3. **Approval or Denial**:
   - If the VC is valid and meets all criteria, the school’s system authorizes Bob to pick up the child.
   - If the VC is expired, revoked, or tampered with, the school system denies the pickup request and can notify Alice of any attempts to use an invalid credential.

### **Step 4: Optional – Revoking or Updating the Authorization**

1. **Revoking the Credential**:
   - If Alice needs to revoke Bob’s authorization, she can do so from her digital wallet.
   - The revocation is recorded on a verifiable data registry (either on-chain or off-chain, depending on the implementation), updating Bob’s credential status to “revoked.”

2. **Updating Authorization**:
   - If Alice wants to extend or change the pickup authorization, she can issue an updated VC to Bob with the new parameters (e.g., extending the validity period).

---

## **Example of the User Flow for Each Participant**

### **For Alice (Parent)**

- Registers with the school, creating a DID.
- Uses a simple app to select delegation options and authorizes Bob as a pickup guardian.
- Issues and sends a VC to Bob with a single click.
- Can review, revoke, or update this credential as needed from the app.

### **For Bob (Guardian)**

- Registers his DID through Alice or the school.
- Receives the VC in his digital wallet, which he brings to the school for verification.
- Scans a QR code or taps an NFC reader at the school to present his VC.
- After the pickup is complete, he receives a confirmation in his wallet for record-keeping.

### **For the School (Verifier)**

- Maintains a verification system to scan and authenticate VCs.
- Reads the presented VC, checks issuer (Alice’s DID) and holder (Bob’s DID) info, and validates the authorization.
- Records successful pickups for audit logs or compliance needs, without storing any unnecessary personal data.

---

## **Advantages of Using DIDs and VCs for Delegation**

1. **Privacy**: Alice controls who has access to her authorization, and the school does not store unnecessary personal data.
2. **Security**: The system verifies credentials cryptographically, reducing the risk of forgery or impersonation.
3. **Control**: Alice can revoke or update Bob’s authorization anytime, keeping her in control.
4. **Simplicity**: Alice and Bob can easily manage their credentials through a user-friendly app.

---

This flow illustrates how DIDs and VCs allow parents to delegate responsibilities without redundant paperwork or repetitive approvals securely. This approach could similarly be adapted to many other scenarios, like authorizing access to financial accounts, sharing healthcare records, or delegating permissions in professional settings.

---

## Various Implementation Approaches

### **Approach 1: Using Verifiable Credential (Preferred Method)**

Alice issues a **Verifiable Credential (VC)** to Bob that delegates specific rights or permissions. This approach maintains the **privacy and flexibility** of Alice’s DID while adhering to DID standards.

1. Alice’s DID document remains general, without directly referencing Bob’s DID.
2. The delegation is captured in a VC issued by Alice to Bob, as shown in the earlier example.

This is the most privacy-preserving and modular approach, as it avoids hardcoding relationships into Alice's DID document and ensures revocability through the VC's status.

### **Approach 2: Including Delegation in DID Document**

If explicitly referencing Bob's DID in Alice's DID document is required in certain cases (e.g., strict governance requirements), a **[capabilityDelegation](https://www.w3.org/TR/did-core/#capability-delegation)** property can be added. This property points to Bob’s DID or a key used by Bob. Reference: https://www.w3.org/TR/did-core/#capability-delegation 

Example:

#### Alice's DID Document with Delegation Capability

```json
{
  "@context": "https://www.w3.org/ns/did/v1",
  "id": "did:example:alice123",
  "verificationMethod": [
    {
      "id": "did:example:alice123#key1",
      "type": "Ed25519VerificationKey2018",
      "controller": "did:example:alice123",
      "publicKeyBase58": "DyVwSE9ZP3h2ZT3..."
    }
  ],
  "authentication": [
    "did:example:alice123#key1"
  ],
  "assertionMethod": [
    "did:example:alice123#key1"
  ],
  "capabilityDelegation": [
    {
      "id": "did:example:bob456#key1",
      "type": "Ed25519VerificationKey2018",
      "controller": "did:example:bob456",
      "publicKeyBase58": "G3T2Yt89LmCZfqw..."
    }
  ]
}
```

---

### Key Considerations

- **Privacy**: Directly referencing Bob’s DID in Alice’s DID document can reduce privacy, as anyone querying Alice's DID document will see the delegation.
- **Flexibility**: The VC-based approach allows Alice to delegate rights to multiple parties dynamically without modifying her DID document.
- **Revocation**: With a VC, Alice can revoke the delegation by updating the VC status. In contrast, modifying the DID document to revoke the delegation is more complex and requires blockchain or registry updates.

---

### Recommendation

**Use the Verifiable Credential (VC) approach** as the preferred method for delegation. It provides flexibility, privacy, and better scalability for real-world use cases. The inclusion of delegation in the DID document should only be used for specialized scenarios where governance or compliance demands an explicit and permanent record.

---

## Appendix

---

Below is the delegation flow from Alice to Bob using DIDs and VCs.

---

### **Delegation Flow Diagram**

```plaintext
+-------------+                 +----------------+                  +----------------+
|  Alice      |                 |  Bob           |                  |  Service/       |
|  (Issuer)   |                 |  (Delegatee)   |                  |  Verifier       |
+-------------+                 +----------------+                  +----------------+
       |                               |                                     |
       | Issue Delegation VC           |                                     |
       +------------------------------>+                                     |
       |                               |                                     |
       |                               |  Present VC to Service              |
       |                               +------------------------------------>+
       |                               |                                     |
       |                               | Verify VC & Execute Action          |
       |                               |<------------------------------------+
       |                               |                                     |
```

---

### **Diagram Explanation**

1. **Alice (Issuer)**:
   - Issues a Verifiable Credential (VC) to Bob, delegating specific permissions or rights.

2. **Bob (Delegatee)**:
   - Receives the VC and uses it to present to the desired service or verifier.

3. **Service/Verifier**:
   - Verifies the VC's authenticity, checks if the rights are valid, and executes the delegated action.

---

### Sample DID Documents

### **Parent DID Document (Alice)**

```json
{
  "@context": "https://www.w3.org/ns/did/v1",
  "id": "did:example:alice123",
  "verificationMethod": [
    {
      "id": "did:example:alice123#key1",
      "type": "Ed25519VerificationKey2018",
      "controller": "did:example:alice123",
      "publicKeyBase58": "DyVwSE9ZP3h2ZT3..."
    }
  ],
  "authentication": [
    "did:example:alice123#key1"
  ],
  "assertionMethod": [
    "did:example:alice123#key1"
  ]
}
```

---

### **Guardian DID Document (Bob)**

```json
{
  "@context": "https://www.w3.org/ns/did/v1",
  "id": "did:example:bob456",
  "verificationMethod": [
    {
      "id": "did:example:bob456#key1",
      "type": "Ed25519VerificationKey2018",
      "controller": "did:example:bob456",
      "publicKeyBase58": "G3T2Yt89LmCZfqw..."
    }
  ],
  "authentication": [
    "did:example:bob456#key1"
  ]
}
```

---

### Sample Verifiable Credential

#### **Credential for Delegation Authorization**

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1"
  ],
  "id": "https://example.com/credentials/1872",
  "type": ["VerifiableCredential", "DelegationAuthorization"],
  "issuer": "did:example:alice123",
  "issuanceDate": "2024-10-28T14:00:00Z",
  "expirationDate": "2024-11-28T14:00:00Z",
  "credentialSubject": {
    "id": "did:example:bob456",
    "authorizationType": "Child Pickup Authorization",
    "restrictions": {
      "days": ["Monday", "Wednesday", "Friday"],
      "hours": "3:00 PM - 5:00 PM"
    }
  },
  "proof": {
    "type": "Ed25519Signature2020",
    "created": "2024-10-28T14:00:00Z",
    "verificationMethod": "did:example:alice123#key1",
    "proofPurpose": "assertionMethod",
    "jws": "eyJhbGciOiJFZERTQSJ9..QngfiThW"
  }
}
```

---

### Explanation of Attributes

1. **DID Document**:
   - Defines the identity of the parent (Alice) and guardian (Bob).
   - Includes public keys for authentication and assertion.

2. **Verifiable Credential**:
   - Issued by Alice’s DID, delegating authority to Bob’s DID.
   - Includes details about the delegation type, restrictions, and validity period.
   - Cryptographically signed by Alice’s private key.

---

### Key Takeaways

- **Privacy**: No sensitive details about Alice or Bob are shared beyond what is necessary.
- **Security**: The school system verifies the authenticity of the VC using cryptographic signatures.
- **Reusability**: This pattern can be applied to other delegation use cases like access to financial accounts or healthcare data sharing.

---

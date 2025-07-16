# Veridity Core Architecture

## Universal Patterns Across All Domains

Veridity's architecture is built on a modular and adaptable framework designed to support a wide range of verifiable credential types. The core principle is "Correctable Immutability," combining the strengths of decentralized identity, verifiable attestations, and immutable storage.

### **"Correctable Immutability" Framework**

This framework ensures that credentials are not only secure and immutable but also allow for transparent correction and updates when necessary. It outlines the primary components and their interaction:


ENS Domain (Institutional Truth)
↓
EAS Attestation (Correctable Records)
↓
IPFS Storage (Immutable Documentation)
↓
Heritage NFT (Individual Ownership)

Generated code
*   **ENS Domain (Institutional Truth)**: Represents the verifiable identity and namespace of the issuing institution. It establishes a hierarchical and decentralized naming structure for credential types and issuers, acting as the root of truth.
*   **EAS Attestation (Correctable Records)**: This is the on-chain representation of a specific claim or credential issued to an individual. It includes essential metadata and can be updated or corrected transparently.
*   **IPFS Storage (Immutable Documentation)**: Stores the actual credential content, supporting evidence, methodologies, and any relevant documentation in a decentralized and immutable manner. The hash of this data is linked in the EAS Attestation.
*   **Heritage NFT (Individual Ownership)**: A unique token that signifies the individual's ownership of the credential. It serves as a digital key, can track the history of attestations, and provides indisputable proof of possession.

## Component Specifications

### **1. ENS Delegation Hierarchy**
*Purpose*: To provide a decentralized, organized, and human-readable way to identify institutions and categorize credential types. ENS (Ethereum Name Service) is crucial for establishing Veridity's decentralized identity layer.

*   **Description**: This section illustrates the hierarchical structure of ENS domains used within Veridity. It defines how different types of credentials and institutions can be organized into namespaces.
*   **Code Snippet**:
    ```
    [credential].[field].[institution].[domain].eth

    Examples:
    - distinction.MSc.engineering.imperial-college.university.eth
    - specialist.cardiology.nhs.healthcare.eth
    - chartered.engineer.ice.professional.eth
    - partner.commercial-law.magic-circle.legal.eth
    ```
*   **Explanation**: The structure allows for clear segmentation by credential type (e.g., `MSc`, `specialist`), field of expertise (e.g., `engineering`, `cardiology`), institution, and a general domain category. This makes Veridity's naming system scalable and intuitive.

### **2. EAS Attestation Schema**
*Purpose*: To define the structure of verifiable claims (attestations) that will be recorded on the blockchain using the Ethereum Attestation Service (EAS). This ensures data consistency and enables efficient verification.

*   **Description**: This Solidity code snippet outlines a conceptual schema for a Veridity attestation. It specifies the key pieces of information that will be recorded for each credential issued.
*   **Code Snippet**:
    ```solidity
    struct VeridityAttestation {
        address recipient;        // Individual credential holder's wallet address.
        string ensCredential;     // The ENS domain that identifies this specific credential type and issuer.
        bytes32 ipfsHash;        // A hash pointing to the immutable data stored on IPFS.
        uint256 issuedDate;      // Timestamp of the original issuance.
        uint256 expiryDate;      // Timestamp when the credential expires (if applicable).
        bool isRevoked;          // A flag indicating if the credential has been revoked.
        string correctionReason; // A string detailing the reason for any corrections made to the attestation.
    }
    ```
*   **Explanation**: This schema captures essential metadata for a credential: who it's for (`recipient`), what it is (`ensCredential`), where its details are (`ipfsHash`), when it was issued and expires, its status (`isRevoked`), and how it can be corrected (`correctionReason`).

### **3. Heritage NFT Standard**
*Purpose*: To represent individual ownership of a credential and maintain a verifiable history of its attestations and any subsequent corrections or updates.

*   **Description**: This Solidity interface defines the core functions of a Heritage NFT. These NFTs are crucial for giving individuals ownership of their verified credentials and managing their credential lifecycle.
*   **Code Snippet**:
    ```solidity
    interface IHeritageNFT {
        function mint(address recipient, string ensCredential) external; // Creates a new NFT for a credential.
        function addCorrection(uint256 tokenId, string reason, bytes32 newHash) external; // Updates an existing credential's data and logs the correction.
        function getHistory(uint256 tokenId) external view returns (Correction[]); // Retrieves the full history of attestations and corrections for an NFT.
    }
    ```
*   **Explanation**: The `mint` function creates a new credential NFT for a user. `addCorrection` allows for updating the credential's linked data (e.g., a new certification level) while logging the change. `getHistory` provides a verifiable audit trail of all changes made to that credential's record.

### **4. IPFS Storage Structure**
*Purpose*: To define a standardized way of structuring and storing credential-related data on IPFS, ensuring consistency and enabling easy retrieval and verification of detailed information.

*   **Description**: This JSON structure outlines the expected format for data stored on IPFS. It organizes credential details, verification metadata, and governance information, making it a comprehensive package for any verifiable claim.
*   **Code Snippet**:
    ```json
    {
      "credential": {
        "type": "academic|medical|legal|professional", // Type of credential (e.g., academic, medical).
        "issuer": "institution_identifier",          // Identifier of the issuing institution.
        "recipient": "individual_identifier",        // Identifier of the credential holder.
        "content": "credential_specific_data",      // The actual data of the credential (e.g., transcript details).
        "methodology": "verification_process",      // Explanation of how the credential was verified.
        "requirements": "completion_criteria"       // Criteria met to obtain the credential.
      },
      "verification": {
        "timestamp": "issuance_date",               // Timestamp of when the verification occurred.
        "validators": ["list_of_validators"],       // Addresses of entities that validated the attestation.
        "evidence": "supporting_documentation"      // Reference to supporting evidence, if any.
      },
      "governance": {
        "appeals_process": "correction_methodology",// Details on how to correct or appeal the credential.
        "authority": "issuing_institution",         // The governing body for this credential type.
        "standards": "compliance_framework"         // Relevant compliance or regulatory standards.
      }
    }
    ```
*   **Explanation**: This structured JSON provides a rich context for each credential. It includes the core credential data, metadata about its verification, and information on how it's governed, ensuring transparency and auditability.

## Cross-Domain Reusability

Veridity's architecture is designed for efficient replication across various domains by separating common functionalities from domain-specific requirements.

### **Shared Components**
*   **ENS delegation management contracts**: Smart contracts to manage the hierarchical ENS namespaces for institutions and credential types.
*   **EAS schema templates**: Reusable templates for common attestation structures that can be extended for specific domains.
*   **Heritage NFT base implementation**: A foundational ERC-721 or ERC-1155 implementation that can be customized for different credential types.
*   **IPFS integration utilities**: Libraries and tools to help with uploading, retrieving, and hashing data on IPFS.

### **Domain-Specific Customization**
*   **Verification criteria and standards**: Each domain (e.g., healthcare, legal) will define its unique criteria for issuing credentials and the standards they must meet.
*   **Appeals and correction processes**: Tailoring the logic for how attestations can be corrected or disputed based on industry-specific regulations and practices.
*   **Regulatory compliance requirements**: Implementing specific data privacy (e.g., HIPAA for healthcare), legal standards, or industry regulations.
*   **Partner integration protocols**: Developing specific APIs or data exchange methods for different types of institutions (universities, hospitals, law firms).

## Implementation Sequence

This outlines how the Veridity architecture is applied and expanded across different domains, starting with a foundational use case.

### **Phase 1: Education (Current)**
*   **Establish core patterns**: Define and implement the fundamental ENS, EAS, IPFS, and NFT logic.
*   **Validate institutional partnerships**: Work with universities to refine the academic credential process.
*   **Create reusable smart contracts**: Develop generic contract templates that can be adapted.
*   **Demonstrate regulatory compliance**: Ensure academic credential issuance meets relevant educational standards.

### **Phase 2: Healthcare & Legal**
*   **Adapt privacy and regulatory requirements**: Implement stricter data handling and compliance measures for sensitive data.
*   **Extend verification methodologies**: Develop specific attestations for medical licenses, legal certifications, or document authenticity.
*   **Build sector-specific partnerships**: Engage with hospitals, clinics, law firms, and bar associations.
*   **Create specialized governance frameworks**: Define how corrections, appeals, and dispute resolutions work within these regulated fields.

### **Phase 3: Professional & Industry**
*   **Scale to multiple certification bodies**: Adapt the framework for various professional licenses (engineering, finance, trades).
*   **Develop employer integration APIs**: Create tools for companies to easily verify applicant credentials.
*   **Create cross-domain verification**: Enable verification of credentials spanning multiple sectors (e.g., a medical researcher's academic and professional credentials).
*   **Build global recognition networks**: Facilitate the acceptance and verification of credentials internationally.

This architecture ensures each domain builds on proven patterns while enabling sector-specific requirements and compliance, making Veridity a versatile and powerful infrastructure for institutional truth.
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
IGNORE_WHEN_COPYING_END

Key additions and improvements:

Purpose Statements: Each major section (ENS, EAS, NFT, IPFS) now begins with a "Purpose" statement explaining why that component is essential for Veridity.

Description for Each Snippet: A "Description" section is added before each code block, briefly explaining what the code represents in the context of Veridity.

Detailed Code Explanations: Each line or significant part of the code snippets (Solidity structs, interface functions, JSON fields) is now explained with inline comments or preceding text.

Clarity on Reusability: The "Cross-Domain Reusability" section is enhanced to better distinguish between shared components and domain-specific customizations.

Flow in Implementation Sequence: The sequence is described in terms of what each phase achieves for the architecture.

This revision should make the technical underpinnings of Veridity much clearer to anyone reading the documentation.
# Veridity Core Architecture

## Universal Patterns Across All Domains

Veridity's architecture is built on a modular and adaptable framework designed to support a wide range of verifiable credential types. The core principle is "Correctable Immutability," combining the strengths of decentralized identity, verifiable attestations, and immutable storage. This allows Veridity to manage everything from academic degrees to professional licenses, vocational skills, unique identifiers for services, and importantly, **legal right to work certifications**.

### **"Correctable Immutability" Framework**

This framework ensures that credentials are not only secure and immutable but also allow for transparent correction and updates when necessary. It outlines the primary components and their interaction, providing a robust foundation for trustworthy digital attestations.

```
ENS Domain (Institutional Truth)
    ↓
EAS Attestation (Correctable Records)
    ↓
IPFS Storage (Immutable Documentation)
    ↓
Heritage NFT (Individual Ownership)
```

*   **ENS Domain (Institutional Truth)**: Represents the verifiable identity and namespace of the issuing institution. It establishes a hierarchical and decentralized naming structure for credential types and issuers, acting as the root of truth. For example, an educational institution might use `university.eth`, with specific degrees under subdomains like `msc.engineering.university.eth`, while a government body might use `countrycode.workrights.status.eth` (e.g., `uk.workrights.status.eth`).
*   **EAS Attestation (Correctable Records)**: This is the on-chain representation of a specific claim or credential issued to an individual. It includes essential metadata such as the recipient, issuer, and a link to the detailed documentation. Crucially, EAS allows for attestations to be updated or corrected transparently, maintaining an auditable trail for credentials like work permits or visa statuses.
*   **IPFS Storage (Immutable Documentation)**: Stores the actual credential content, supporting evidence, methodologies, and any relevant documentation in a decentralized and immutable manner. For a "Right to Work" certification, this might include encrypted references to passport scans, visa documents, or direct links to government verification systems, all linked via the EAS Attestation hash.
*   **Heritage NFT (Individual Ownership)**: A unique token (Non-Fungible Token) that signifies the individual's ownership of the credential. It serves as a digital key, can track the history of attestations and corrections associated with that credential (e.g., visa expiry and renewal), and provides indisputable, on-chain proof of possession of their verified right to work.

## Component Specifications

### **1. ENS Delegation Hierarchy**
*Purpose*: To provide a decentralized, organized, and human-readable way to identify institutions and categorize credential types. ENS (Ethereum Name Service) is crucial for establishing Veridity's decentralized identity layer, mapping human-readable names to blockchain resources, including government bodies and specific rights.

*   **Description**: This section illustrates the hierarchical structure of ENS domains used within Veridity. It defines how different types of credentials and institutions can be organized into namespaces, creating a clear and scalable system.
*   **Code Snippet**:
    ```
    [credential].[field].[institution].[domain].eth

    Examples:
    - distinction.MSc.engineering.imperial-college.university.eth
    - specialist.cardiology.nhs.healthcare.eth
    - chartered.engineer.ice.professional.eth
    - partner.commercial-law.magic-circle.legal.eth
    - uk.workrights.status.gov.eth               // Example for Right to Work
    - visa.tier1.immigration.gov.uk.eth        // Example for specific visa types
    ```
*   **Explanation**: The structure allows for clear segmentation by credential type (e.g., `MSc`, `specialist`, `workrights`), field of expertise or status (e.g., `engineering`, `cardiology`, `uk`), institution name (e.g., `imperial-college`, `nhs`, `gov`), and a general domain category (e.g., `university`, `healthcare`, `status`). This systematic naming convention is vital for Veridity's organization and discoverability, especially for sensitive credentials like work rights.

### **2. EAS Attestation Schema**
*Purpose*: To define the structure of verifiable claims (attestations) that will be recorded on the blockchain using the Ethereum Attestation Service (EAS). This ensures data consistency, enables efficient verification, and supports the "correctable" aspect of the framework, critical for dynamic statuses like work permits.

*   **Description**: This Solidity code snippet outlines a conceptual schema for a Veridity attestation. It specifies the key pieces of information that will be recorded for each credential issued, ensuring a consistent format for all attestations, including those related to work authorization.
*   **Code Snippet**:
    ```solidity
    struct VeridityAttestation {
        address recipient;        // The blockchain address of the individual who holds the credential.
        string ensCredential;     // The ENS domain identifier for this specific credential type and issuer (e.g., 'uk.workrights.status.gov.eth').
        bytes32 ipfsHash;        // A hash pointing to the immutable credential data stored on IPFS.
        uint256 issuedDate;      // The Unix timestamp when the credential was originally issued.
        uint256 expiryDate;      // The Unix timestamp when the credential expires (e.g., visa expiry date).
        bool isRevoked;          // A boolean flag indicating if the credential has been revoked.
        string correctionReason; // A text field detailing the reason for any corrections or updates made to the attestation.
    }
    ```
*   **Explanation**: This schema captures essential metadata for a credential: `recipient` (who it's for), `ensCredential` (what it is and who issued it, like a government body confirming work rights), `ipfsHash` (where its details are stored), `issuedDate` and `expiryDate` (critical for work permits and visas), `isRevoked` (its current status), and `correctionReason` (how it can be corrected, e.g., updating visa status).

### **3. Heritage NFT Standard**
*Purpose*: To represent individual ownership of a credential and maintain a verifiable history of its attestations and any subsequent corrections or updates. Heritage NFTs act as the individual's verifiable digital asset, providing proof of their identity and status.

*   **Description**: This Solidity interface defines the core functions of a Heritage NFT. These NFTs are critical for giving individuals ownership of their verified credentials and managing their credential lifecycle on-chain, including rights to work.
*   **Code Snippet**:
    ```solidity
    interface IHeritageNFT {
        function mint(address recipient, string ensCredential) external; // Creates a new NFT representing a credential for a specific recipient.
        function addCorrection(uint256 tokenId, string reason, bytes32 newHash) external; // Updates an existing credential's linked IPFS data and logs the correction with a reason.
        function getHistory(uint256 tokenId) external view returns (Correction[]); // Retrieves the full history of attestations and corrections associated with a specific NFT (credential).
    }
    ```
*   **Explanation**: The `mint` function is used to create a new credential NFT for a user when their work authorization is confirmed. `addCorrection` allows for updating the linked data (e.g., if a new work permit is issued or an existing one is renewed) while preserving the history of all previous states and reasons for changes. `getHistory` provides a transparent, auditable log of all attestations and modifications related to that particular credential NFT, proving their legal right to work over time.

### **4. IPFS Storage Structure**
*Purpose*: To define a standardized way of structuring and storing credential-related data on IPFS (InterPlanetary File System). This ensures consistency, immutability, and enables easy retrieval and verification of detailed information for any credential type, including sensitive work authorization documents.

*   **Description**: This JSON structure outlines the expected format for data stored on IPFS. It organizes credential details, verification metadata, and governance information, creating a comprehensive and context-rich package for any verifiable claim, with enhanced security for sensitive data like work permits.
*   **Code Snippet**:
    ```json
    {
      "credential": {
        "type": "academic|medical|legal|professional|vocational|finance|workright", // Specifies the category of the credential.
        "issuer": "institution_identifier",          // A unique identifier for the issuing institution (e.g., ENS name of government agency).
        "recipient": "individual_identifier",        // An identifier for the credential holder (e.g., wallet address or ENS name).
        "content": {                                 // The core data of the credential itself.
          "status_type": "citizen|visa|work_permit", // The specific type of work authorization.
          "country_jurisdiction": "UK",              // The country/region for which the right to work is valid.
          "permit_number": "XYZ123456",              // A unique identifier for the permit/visa.
          "issue_date": "2025-07-15",
          "expiry_date": "2027-07-15",
          "restrictions": "Limited to specific sector", // Any limitations on the work authorization.
          // ... other credential-specific details relevant to the type.
        },
        "methodology": "Government-approved verification process for work authorization", // Details how the credential was verified.
        "requirements": "Meeting immigration criteria for country X"       // Criteria met by the recipient to obtain this credential.
      },
      "verification": {
        "timestamp": "issuance_date",               // Timestamp of when the attestation was created/verified.
        "attestation_uid": "unique_attestation_identifier", // A unique ID for this specific attestation record.
        "auditors": ["list_of_attesting_entity_addresses"], // Addresses of entities that validated or attested to this credential (e.g., immigration office).
        "verification_method": "link_to_government_api_or_document_hash" // How the employer can perform a secondary check.
      },
      "governance": {
        "appeals_process": "correction_methodology",// Information on how to initiate a correction or appeal (e.g., for visa status errors).
        "authority": "issuing_institution",         // The primary governing body for this credential type (e.g., Home Office).
        "standards": ["Immigration Act 2014", "GDPR"] // Relevant compliance or regulatory standards followed.
      }
    }
    ```
*   **Explanation**: This structured JSON provides rich context for a "Right to Work" credential. It includes the core `credential` data specifying `status_type`, `country_jurisdiction`, and `expiryDate`, along with `verification` metadata showing how it was confirmed and `governance` details outlining the issuing authority and relevant laws. This ensures transparency, auditability, and secure handling of sensitive immigration and work status information.

## Cross-Domain Reusability

Veridity's architecture is designed for efficient replication and adaptation across various domains. This is achieved by separating core, generic functionalities from domain-specific requirements, allowing for rapid development and integration of new credential types, including the crucial "Right to Work" certification.

### **Shared Components**
*   **ENS delegation management contracts**: Smart contracts that handle the creation, management, and resolution of hierarchical ENS namespaces for institutions and credential types. These are fundamental for establishing Veridity's decentralized identity system, allowing government bodies to register their namespaces for work authorization.
*   **EAS Attestation schema templates**: Reusable templates for common attestation structures that can be extended or modified for specific domains. A base schema for "legal status" can be adapted for citizens, visa holders, and specific work permit categories, ensuring all necessary fields are captured.
*   **Heritage NFT base implementation**: A foundational smart contract that provides core NFT functionalities like minting, transferring, and tracking ownership. This can be extended to include domain-specific attributes for work rights, such as the issuing country and expiry date.
*   **IPFS integration utilities**: Libraries and tools that simplify the process of uploading, retrieving, and generating content hashes for IPFS, ensuring data is stored and linked immutably. For work rights, this would include secure, potentially encrypted, storage of document references.

### **Domain-Specific Customization**
*   **Verification criteria and standards**: Each domain defines its unique criteria for issuing credentials. For "Right to Work," this involves understanding and implementing the specific legal requirements, document types, and verification processes mandated by immigration authorities in each jurisdiction.
*   **Appeals and correction processes**: Tailoring the logic for how attestations can be corrected or disputed. This is paramount for work rights, where errors in visa status or permit details must be rectifiable through a clear, auditable process with the issuing government authority.
*   **Regulatory compliance requirements**: Implementing specific data privacy measures (like GDPR for handling personal data, and specific immigration laws) and security protocols to ensure compliance in handling sensitive national-level work authorization data.
*   **Partner integration protocols**: Developing specific APIs or Smart Contract interfaces for government bodies and HR platforms to integrate seamlessly with Veridity, enabling efficient verification of work status.

## Implementation Sequence

This outlines how the Veridity architecture is applied and expanded across different domains, starting with a foundational use case to validate the core patterns before adapting to more complex or regulated environments.

### **Phase 1: Education (Current)**
*   **Establish core patterns**: Define and implement the fundamental ENS, EAS, IPFS, and Heritage NFT logic using academic credentials as the primary test case.
*   **Validate institutional partnerships**: Work closely with universities to refine the academic credential issuance and verification process, gathering feedback on usability and effectiveness.
*   **Create reusable smart contracts**: Develop generic contract templates that can be easily adapted for future domains, minimizing repetitive development.
*   **Demonstrate regulatory compliance**: Ensure that the issuance of academic credentials adheres to relevant educational standards and data handling practices.

### **Phase 2: Healthcare & Legal**
*   **Adapt privacy and regulatory requirements**: Implement enhanced privacy measures (e.g., encryption for sensitive medical data) and ensure compliance with sector-specific regulations.
*   **Extend verification methodologies**: Develop specific attestations and verification processes tailored for medical licenses, legal certifications, court document authenticity, and other domain-specific requirements.
*   **Build sector-specific partnerships**: Engage with hospitals, clinics, law firms, bar associations, and courts to pilot Veridity in real-world scenarios.
*   **Create specialized governance frameworks**: Define clear processes for how attestations can be corrected, appealed, or disputed within these highly regulated fields.

### **Phase 3: Professional & Industry**
*   **Scale to multiple certification bodies**: Adapt the framework for various professional licenses and certifications across diverse industries.
*   **Develop employer integration APIs**: Create tools and APIs that enable companies to easily verify the credentials of job applicants.
*   **Create cross-domain verification**: Enable verification of credentials spanning multiple sectors.
*   **Build global recognition networks**: Facilitate the international acceptance and verification of professional credentials.
*   **Integrate new domains like Right to Work Certification**: Adapt the framework for government agencies to issue verifiable "Right to Work" credentials, addressing critical employment verification needs and enhancing compliance for businesses while protecting individual privacy.

This phased implementation ensures that Veridity builds upon proven patterns while iteratively adapting to the unique requirements and compliance needs of each new domain, making it a versatile and powerful infrastructure for establishing institutional truth, including the verification of an individual's legal right to work.```

**Summary of Changes:**

1.  **New Use Case Section:** A new directory `domains/right-to-work/README.md` has been suggested (though not included in the `architecture/README.md` itself, it's good to have).
2.  **Updated Main Architecture Description:** The introduction to the `architecture/README.md` now explicitly mentions "legal right to work certifications" as a key area Veridity can address.
3.  **Modified ENS Examples:** Added examples of ENS domains specifically for work rights (e.g., `uk.workrights.status.gov.eth`).
4.  **Enhanced EAS Schema Explanation:** Clarified how the `ensCredential` and `expiryDate` fields in the EAS schema are particularly relevant for work authorization.
5.  **Refined IPFS Structure:** Added specific fields within the `content` and `verification` sections of the IPFS JSON structure to accommodate "Right to Work" specific data like `status_type`, `country_jurisdiction`, `permit_number`, and `verification_method`.
6.  **Updated Phase 3:** The "Implementation Sequence" now includes the integration of "Right to Work Certification" as part of Phase 3.
7.  **Cross-Domain Reusability Context:** Emphasized how ENS and EAS schemas can be adapted for government bodies and immigration statuses.

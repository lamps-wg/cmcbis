---
title: "Certificate Management Messages over CMS (CMC): Compliance Requirements"
abbrev: "CMC: Compliance"
category: std
obsoletes: 5274, 6402

docname: draft-ietf-lamps-rfc5274bis-latest
submissiontype: IETF
ipr: pre5378Trust200902
number:
date:
consensus: true
v: 3
area: SEC
workgroup: LAMPS Working Group
keyword:
 - Public Key Infrastructure
 - Cryptographic Message Syntax
 - Certificate Management
 - Compliance
venue:
  group: WG
  type: LAMPS
  mail: spasm@ietf.org
  arch: https://mailarchive.ietf.org/arch/browse/spasm/
  github: TBD

author:
 -
    ins: J. Mandel, Ed.
    name: Joseph Mandel
    org: AKAYLA, Inc.
    email: joe@akayla.com
 -
    ins: S. Turner, Ed.
    name: Sean Turner
    organization: sn3rd
    email: sean@sn3rd.com

contributor:
 -
   name: Jim Schaad
   org: August Cellars
 -
   name: Michael Myers
   org: TraceRoute Security, Inc.

normative:
  CMC-STRUCT: I-D.ietf-lamps-rfc5272bis
  CMC-TRANS: I-D.ietf-lamps-rfc5273bis
  CMS: RFC5652
  CMS-AES: RFC3565
  CMS-ALG: RFC3370
  CMS-DH: RFC2631
  CRMF: RFC4211
  CMS-RSA-OAEP: RFC3560
  CMS-RSA-PSS: RFC4056
  DH-POP: RFC6955
  RSA-256: RFC4055
  PBKDF2: RFC2898
  AES-WRAP: RFC3394

informative:
  PKCS10: RFC2986
  SMALL-SUB-GROUP: RFC2785
  HASH-ATTACKS: RFC4270
  CMC-COMPv1: I-D.ietf-lamps-rfc5274bis
  CMC-Updates: RFC6402

--- abstract

This document provides a set of compliance statements about the CMC
(Certificate Management over CMS) enrollment protocol.  The ASN.1
structures and the transport mechanisms for the CMC enrollment
protocol are covered in other documents.  This document provides the
information needed to make a compliant version of CMC.

This document obsoletes RFCs 5274 and 6402.

--- middle

# Introduction

The CMC (Certificate Management over CMS) protocol is designed in
terms of a client/server relationship.  In the simplest case, the
client is the requestor of the certificate (i.e., the End Entity
(EE)) and the server is the issuer of the certificate (i.e., the
Certification Authority (CA)).  The introduction of a Registration
Authority (RA) into the set of agents complicates the picture only
slightly.  The RA becomes the server with respect to the certificate
requestor, and it becomes the client with respect to the certificate
issuer.  Any number of RAs can be inserted into the picture in this
manner.

The RAs may serve specialized purposes that are not currently covered
by this document.  One such purpose would be a Key Escrow agent.  As
such, all certificate requests for encryption keys would be directed
through this RA and it would take appropriate action to do the key
archival.  Key recovery requests could be defined in the CMC
methodology allowing for the Key Escrow agent to perform that
operation acting as the final server in the chain of agents.

If there are multiple RAs in the system, it is considered normal that
not all RAs will see all certificate requests.  The routing between
the RAs may be dependent on the content of the certificate requests
involved.

This document is divided into six sections, each section specifying
the requirements that are specific to a class of agents in the CMC
model.  These are 1) all Entities, 2) all Servers, 3) all Clients, 4)
all End-Entities, 5) all Registration Authorities, 6) all Certification
Authorities.

This document obsoletes {{CMC-COMPv1}} and {{CMC-Updates}}.

# Terminology

There are several different terms, abbreviations, and acronyms used
in this document that we define here for convenience and consistency
of usage:

End-Entity (EE):
: Refers to the entity that owns a key pair and for
  whom a certificate is issued.

Registration Authority (RA) or Local RA (LRA):

: Refers to an entity
  that acts as an intermediary between the EE and the CA.  Multiple
  RAs can exist between the End-Entity and the Certification
  Authority.  RAs may perform additional services such as key
  generation or key archival.  This document uses the term RA for
  both RA and LRA.

Certification Authority (CA):

: Refers to the entity that issues
  certificates.

Client:

: Refers to an entity that creates a PKI Request.  In this
  document, both RAs and EEs can be clients.

Server:

: Refers to the entities that process PKI Requests and create
  PKI Responses.  In this document both CAs and RAs can be servers.

PKCS #10:

: Refers to the Public Key Cryptography Standard #10
  {{PKCS10}}, which defines a certification request syntax.

CRMF:

: Refers to the Certificate Request Message Format RFC {{CRMF}}.
CMC uses this certification request syntax defined in this
document as part of the protocol.

CMS:

: Refers to the Cryptographic Message Syntax RFC {{CMS}}.  This
  document provides for basic cryptographic services including
  encryption and signing with and without key management.

PKI Request/Response:

: Refers to the requests/responses described in
  this document.  PKI Requests include certification requests,
  revocation requests, etc.  PKI Responses include certs-only
  messages, failure messages, etc.

Proof-of-Identity:

: Refers to the client proving they are who they say
  that they are to the server.

Proof-of-Possession (POP):

: Refers to a value that can be used to
  prove that the private key corresponding to a public key is in the
  possession and can be used by an end-entity.

Transport wrapper:

: Refers to the outermost CMS wrapping layer.

# Requirements Terminology

{::boilerplate bcp14-tagged}

# Changes since RFC 5274 and 6402

<aside markdown="block">
Note: For now, this section will be list of the changes introduced
  by each version. After WGLC, this section will be finalized.
</aside>

TODO for -03 WG version:

* Add cryptographic algorithm requirements

-01 WG version changes:

* Reformat cryptographic algorithm section

-01 WG version changes:

* Updated references

-00 WG version changes:

* Added pre-5378 boilerplate

-02 individual version changes:

* Updated text in intro
* Changed "all agents" to "all entities" in overview
* Updated section header numbering

-01 individual version changes:

* Changed RFC 5272 references to draft-mandel-lamps-rfc5272bis
* Changed RFC 5273 references to draft-mandel-lamps-rfc5273bis

-00 individual version changes:

* Added "Changes Since 5274 and 6402" section
* Updated references
* Merged {{CMC-Updates}} text
* Updated and moved Acknowledgments

# Requirements for All Entities

All {{CMC-STRUCT}} and {{CMC-TRANS}} compliance statements MUST be
adhered to unless specifically stated otherwise in this document.

All entities MUST support Full PKI Requests, Simple PKI Responses,
and Full PKI Responses.  Servers SHOULD support Simple PKI Requests.

All entities MUST support the use of the CRMF syntax for
certification requests.  Support for the PKCS #10 syntax for
certification requests SHOULD be implemented by servers.

The extendedFailInfo field SHOULD NOT be populated in the
CMCStatusInfoV2 object; the failInfo field SHOULD be used to relay
this information.  If the extendedFailInfo field is used, it is
suggested that an additional CMCStatusInfoV2 item exist for the same
body part with a failInfo field.

All entities MUST implement the HTTP transport mechanism as defined
in {{CMC-TRANS}}.  Other transport mechanisms MAY be implemented.

## Cryptographic Algorithm Requirements

The following table shows the algorithm requirements that must be used for SignedData and AuthenticatedData.

Description of the columns in the table:

Use: Description of the key usage
Mandatory: Algorithms that MUST be supported by conforming implementations
Recommend: Algorithms that SHOULD be supported
Optional: Algorithms that MAY be supported

| Use      | Mandatory    | Recommend | Optional |
|:-----------------------|:-----------|:-------------|
| Verify signature in SignedData | TBD | TBD | other algorithms |
| Generate signature for SignedData | TBD | TBD | other algorithms |
| Content encryption for EnvelopedData | TBD | TBD | other algorithms |
| Key transport for EnvelopedData | TBD | TBD | other algorithms |
|:-----------------------|:-----------|:-------------|:-------------|
{: #AlgReq-SD-and-AD title="Algorithm Requirements for SignedData and AuthenticatedData"}

The following table shows the algorithm requirements for EnvelopedData and AuthenticatedData if supported by the entity.

Description of the columns in the table:

Use: Description of key usage
Mandatory: Algorithms that MUST be supported by conforming implementations
Recommend: Algorithms that SHOULD be supported
Optional: Algorithms that MAY be supported

| Use      | Mandatory    | Recommend | Optional |
|:-----------------------|:-----------|:-------------|
| key agreement for EnvelopedData | TBD | TBD | TBD |
| PasswordRecipientInfo for EnvelopedData or AuthenticatedData | TBD | TBD | TBD |
| AuthenticatedData | PasswordRecipientInfo | TBD | TBD |
|:-----------------------|:-----------|:-------------|:-------------|
{: #AlgReq-ED-and-AD title="Algorithm Requirements for EnvelopedData and AuthenticatedData"}

The following table shows the algorithm requirements for Controls.

Description of the columns in the table:

Control: Control carried as part of Full PKI Requests and Responses
AlgId: Notes the algorithm identifier which is used
Mandatory: Algorithms that MUST be supported by conforming implementations
Recommend: Algorithms that SHOULD be supported
Optional: Algorithms that MAY be supported

| Control | AlgId      | Mandatory    | Recommend | Optional |
|:-----------------------|:-----------|:-------------|:-------------|:-------------|
| Identity Proof Version 2 control | hashAlgId | TBD | TBD | TBD |
| Identity Proof Version 2 control | macAlgId | TBD | TBD | TBD |
| Pop Link Witness Version 2 control | keyGenAlgorithm | TBD | TBD | TBD |
| Pop Link Witness Version 2 control | macAlgorithm | TBD | TBD | TBD |
| Encrypted POP and Decrypted POP controls | witnessAlgID | TBD | TBD | TBD |
| Encrypted POP and Decrypted POP controls | thePOPAlgID | TBD | TBD | TBD |
| Publish Trust Anchors control | hashAlgorithm | TBD | TBD | TBD |
|:-----------------------|:-----------|:-------------| :-------------|:-------------|
{: #AlgReq-Controls title="Algorithm Requirements for Controls"}

The following table shows the algorithm requirements for Proof of Possession (POP) of DH Certification Requests and the No-Signature mechanism.

Description of the columns in the table:

Use: Request type from Appendix C of {{CMC-STRUCT}}
Mandatory: Algorithms that MUST be supported by conforming implementations
Recommend: Algorithms that SHOULD be supported
Optional: Algorithms that MAY be supported

|Use | Mandatory | Recommend | Optional |
|:-----------------------|:-----------------------|:-----------|:-------------|
| EE generates DH keys for certification | EE and CA/RA {{Section 4 of DH-POP}} | {TBD} | EE and CA/RA {{Section 3 of DH-POP}} |
| No-Signature Signature Mechanism | Appendix C of {{CMC-STRUCT}} | {TBD} | {TBD} |
|:-----------------------|:-----------|:-------------| :-------------|
{: #AlgReq-DH-and-NS title="Algorithm Requirements for DH Certification Requests and the No-Signature mechanism"}

## Controls

The following table lists the name and level of support required for
each control.

| Control                    | EE       | RA       | CA       |
|:---------------------------|:---------|:---------|:---------|
| Extended CMC Status Info   | MUST     | MUST     | MUST     |
| CMC Status Info            | SHOULD   | SHOULD   | SHOULD   |
| Identity Proof Version 2   | MUST     | MUST     | MUST     |
| Identity Proof             | SHOULD   | SHOULD   | SHOULD   |
| Identification             | MUST     | MUST     | MUST     |
| POP Link Random            | MUST     | MUST     | MUST     |
| POP Link Witness Version 2 | MUST     | MUST     | MUST     |
| POP Link Witness           | SHOULD   | MUST     | MUST     |
| Data Return                | MUST     | MUST     | MUST     |
| Modify Cert Request        | N/A      | MUST     | (2)      |
| Add Extensions             | N/A      | MAY      | (1)      |
| Transaction ID             | MUST     | MUST     | MUST     |
| Sender Nonce               | MUST     | MUST     | MUST     |
| Recipient Nonce            | MUST     | MUST     | MUST     |
| Encrypted POP              | (4)      | (5)      | SHOULD   |
| Decrypted POP              | (4)      | (5)      | SHOULD   |
| RA POP Witness             | N/A      | SHOULD   | (1)      |
| Get Certificate            | optional | optional | optional |
| Get CRL                    | optional | optional | optional |
| Revocation Request         | SHOULD   | SHOULD   | MUST     |
| Registration Info          | SHOULD   | SHOULD   | SHOULD   |
| Response Information       | SHOULD   | SHOULD   | SHOULD   |
| Query Pending              | MUST     | MUST     | MUST     |
| Confirm Cert.  Acceptance  | MUST     | MUST     | MUST     |
| Publish Trust Anchors      | (3)      | (3)      | (3)      |
| Authenticate Data          | (3)      | (3)      | (3)      |
| Batch Request              | N/A      | MUST     | (2)      |
| Batch Responses            | N/A      | MUST     | (2)      |
| Publication Information    | optional | optional | optional |
| Control Processed          | N/A      | MUST     | (2)      |
| RA Identity Proof Witness  | N/A      | MUST     | (2)      |
| Response Body              | (6)      | (6)      | N/A.     |
{: #cmc-controls title="CMC Control Attributes"}

Notes:

1. CAs SHOULD implement this control if designed to work with RAs.

2. CAs MUST implement this control if designed to work with RAs.

3. Implementation is optional for these controls.  We strongly
   suggest that they be implemented in order to populate client
   trust anchors.

4. EEs only need to implement this if (a) they support key agreement
   algorithms or (b) they need to operate in environments where the
   hardware keys cannot provide POP.

5. RAs SHOULD implement this if they implement RA POP Witness.

6. EE's SHOULD implement if designed to work with RAs and MUST
   implement if intended to be used in environments where RAs are
   used for identity validation or key generation.  RAs SHOULD
   implement and validate responses for consistency.

Strong consideration should be given to implementing the Authenticate
Data and Publish Trust Anchors controls as this gives a simple method
for distributing trust anchors into clients without user
intervention.

## CRMF Feature Requirements

The following additional restrictions are placed on CRMF features:

The registration control tokens id-regCtrl-regToken and id-regCtrl-
authToken MUST NOT be used.  No specific CMC feature is used to
replace these items, but generally the CMC controls identification
and identityProof will perform the same service and are more
specifically defined.

The control token id-regCtrl-pkiArchiveOptions SHOULD NOT be
supported.  An alternative method is under development to provide
this functionality.

The behavior of id-regCtrl-oldCertID is not presently used.  It is
replaced by issuing the new certificate and using the id-cmc-
publishCert to remove the old certificate from publication.  This
operation would not normally be accompanied by an immediate
revocation of the old certificate; however, that can be accomplished
by the id-cmc-revokeRequest control.

The id-regCtrl-protocolEncrKey is not used.

# Requirements for Clients

There are no additional requirements.


# Requirements for Servers

There are no additional requirements.


# Requirements for EEs

If an entity implements Diffie-Hellman, it MUST implement either the
DH-POP Proof-of-Possession as defined in {{Section 4 of DH-POP}} or the
challenge-response POP controls id-cmc-encryptedPOP and id-cmc-
decryptedPOP.


# Requirements for RAs

RAs SHOULD be able to do delegated POP.  RAs implementing this
feature MUST implement the id-cmc-lraPOPWitness control.

All RAs MUST implement the promotion of the id-aa-cmc-unsignedData as
covered in {{Section 3.2.3 of CMC-STRUCT}}.


# Requirements for CAs

Providing for CAs to work in an environment with RAs is strongly
suggested.  Implementation of such support is strongly suggested as
this permits the delegation of substantial administrative interaction
onto an RA rather than at the CA.

CAs MUST perform at least minimal checks on all public keys before
issuing a certificate.  At a minimum, a check for syntax would occur
with the POP operation.  Additionally, CAs SHOULD perform simple
checks for known bad keys such as small subgroups for DSA-SHA1 and DH
keys {{SMALL-SUB-GROUP}} or known bad exponents for RSA keys.

CAs MUST enforce POP checking before issuing any certificate.  CAs
MAY delegate the POP operation to an RA for those cases where 1) a
challenge/response message pair must be used, 2) an RA performs
escrow of a key and checks for POP in that manner, or 3) an unusual
algorithm is used and that validation is done at the RA.

CAs SHOULD implement both the DH-POP Proof-of-Possession as defined
in {{Section 4 of DH-POP}} and the challenge-response POP controls id-
cmc-encryptedPOP and id-cmc-decryptedPOP.


# Security Considerations

This document uses {{CMC-STRUCT}} and {{CMC-TRANS}} as building blocks to
this document.  The security sections of those two documents are
included by reference.

Knowledge of how an entity is expected to operate is vital in
determining which sections of requirements are applicable to that
entity.  Care needs to be taken in determining which sections apply
and fully implementing the necessary code.

Cryptographic algorithms have and will be broken or weakened.
Implementers and users need to check that the cryptographic
algorithms listed in this document make sense from a security level.
The IETF from time to time may issue documents dealing with the
current state of the art.  Two examples of such documents are
{{SMALL-SUB-GROUP}} and {{HASH-ATTACKS}}.


--- back


# Acknowledgements
{:numbered="false"}

Obviously, the authors of this version of the document would like to
thank Jim Schaad and Michael Myers for their work on the previous
version of this document.

The acknowledgment from the previous version of this document follows:

The authors and the PKIX Working Group are grateful for the
participation of Xiaoyi Liu and Jeff Weinstein in helping to author
the original versions of this document.

The authors would like to thank Brian LaMacchia for his work in
developing and writing up many of the concepts presented in this
document.  The authors would also like to thank Alex Deacon and Barb
Fox for their contributions.

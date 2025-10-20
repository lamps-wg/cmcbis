---
title: "Certificate Management over CMS (CMC): Transport Protocols"
abbrev: "CMC: Transport Protocols"
category: std
obsoletes: 5273, 6402

docname: draft-ietf-lamps-rfc5273bis-latest
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
 - Transport Protocols
venue:
  group: WG
  type: LAMPS
  mail: spasm@ietf.org
  arch: https://mailarchive.ietf.org/arch/browse/spasm/
  github: "seanturner/cmcbis"

author:
 -
    ins: J. Mandel, Ed.
    name: Joseph Mandel
    org: AKAYLA, Inc.
    email: joe@akayla.com
 -
    ins: S. Turner, Ed.
    name: Sean Turner (editor)
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
  BCP195: RFC9325
  CMC-STRUCT: I-D.ietf-lamps-rfc5272bis
  HTTP: RFC9110
  SMIMEV4: RFC8551
  X690:
    target: https://www.itu.int/rec/T-REC-X.690
    title: >
      Information Technology -- Abstract Syntax Notation One (ASN.1):
      ASN.1 encoding rules: Specification of Basic Encoding Rules (BER),
      Canonical Encoding Rules (CER) and Distinguished Encoding Rules (DER)
    date: 2021-02
    author:
    -  org: ITU-T
    seriesinfo:
      ITU-T Recommendation: X.690
      ISO/IEC: 8825-1:2021

informative:
  TLS: RFC5246
  CMC-TRANSv1: RFC5273
  CMC-Updates: RFC6402
  IPsec: RFC4301
  HTTP/1.0: RFC1945
  HTTP/1.1: RFC9112
  HTTP/2: RFC9113
  HTTP/3: RFC9114
  COOKIES: RFC6265
  erratum3593:
    title: "RFC 5273 erratum 3593"
    target: https://www.rfc-editor.org/errata/eid3593
    date: 2013-04

--- abstract

This document defines a number of transport mechanisms that are used
to move CMC (Certificate Management over CMS (Cryptographic Message
Syntax)) messages.  The transport mechanisms described in this
document are HTTP, file, mail, and TCP.

This document obsoletes RFC 5273 and RFC 6402.


--- middle

# Introduction

This document defines a number of transport methods that are used to
move CMC messages (defined in {{CMC-STRUCT}}).  The transport
mechanisms described in this document are HTTP, file, mail, and TCP.

This document obsoletes RFC 5273 {{CMC-TRANSv1}} and RFC 6402 {{CMC-Updates}}. This
document also incorporates {{erratum3593}}.


# Requirements Terminology

{::boilerplate bcp14-tagged}


# Changes Since 5273 and 6402

Merged {{CMC-Updates}} text.

IANA assigned TCP port 5318 for the use of CMC.

Clarified the file extensions for Full PKI Requests and Responses.


Replaced TLS 1.0 with TLS 1.2 or later, and added that implemetations are
required to follow the recommendations in {{!BCP195}}.

Addressed {{erratum3593}}.

# File-Based Protocol

Enrollment messages and responses may be transferred between clients
and servers using file-system-based mechanisms, such as when
enrollment is performed for an off-line client.  When files are used
to transport Full PKI Request or Full PKI Response messages,
there MUST be only one instance of a request or response message in a
single file and the file MUST be binary encoded. The abbreviations crq
and crp stand for Full PKI Request/Response,
respectively; for clarity we define file extensions for them. The
following file type extensions SHOULD be used:


| Message Type        | File Extension |
|:--------------------|:---------------+
| Simple PKI Request  | .p10           |
| Full PKI Request    | .crq           |
| Simple PKI Response | .p7c           |
| Full PKI Response   | .crp           |
{: #file-id title="File PKI Request/Response Identification"}

# Mail-Based Protocol

MIME wrapping is defined for those environments that are MIME native.
The basic mime wrapping in this section is taken from {{SMIMEV4}}.
When using a mail-based protocol, MIME wrapping between the layers of
CMS wrapping is optional.  Note that this is different from the
standard S/MIME (Secure MIME) message.

Simple enrollment requests are encoded using the "application/pkcs10"
content type.  A file name MUST be included either in a Content-Type
or a Content-Disposition statement.  The extension for the file MUST
be ".p10".

Simple enrollment response messages MUST be encoded as content type
"application/pkcs7-mime".  A smime-type parameter MUST be on the
Content-Type statement with a value of "certs-only".  A file name
with the ".p7c" extension MUST be specified as part of the
Content-Type or Content-Disposition statement.

Full enrollment request messages MUST be encoded as content type
"application/pkcs7-mime".  The smime-type parameter MUST be included
with a value of "CMC-Request".  A file name with the ".p7m" extension
MUST be specified as part of the Content-Type or Content-Disposition
statement.

Full enrollment response messages MUST be encoded as content type
"application/pkcs7-mime".  The smime-type parameter MUST be included
with a value of "CMC-Response".  A file name with the ".p7m"
extension MUST be specified as part of the Content-Type or
Content-Disposition statement.

| Item         | MIME Type              | File Extension      | SMIME Type   |
|:-------------|:-----------------------|:-----------|:-------------|
| Simple PKI Request  | application/pkcs10     | .p10       | N/A          |
| Full PKI Request    | application/pkcs7-mime | .p7m       | CMC-Request  |
| Simple PKI Response  | application/pkcs7-mime | .p7c       | certs-only   |
| Full PKI Response     | application/pkcs7-mime | .p7m       | CMC-Response |
{: #mime-id title="MIME PKI Request/Response Identification"}


# HTTP-Based Protocol

This section describes the conventions for use of HTTP {{HTTP}} as a
transport layer. The use of HTTPS {{HTTP}} provides any necessary
content protection from eavesdroppers.

In order for CMC clients and servers using HTTP to interoperate, the
following rules apply.

> Client requests are submitted by use of the POST method.

> Servers MUST use the 200 response code for successful responses.

> Clients MAY attempt to send certification requests using HTTPS {{HTTP}},
although servers are not required to support TLS/QUIC but a secure channel
might be available regardless depending on the HTTP version implemented
{{HTTP/1.0}}, {{HTTP/1.1}}, {{HTTP/2}}, {{HTTP/3}}, or later. If TLS is used by the HTTP version, then the
implementation MUST follow the recommendations in {{BCP195}}. CMC implementations
that support TLS 1.3 or QUIC MUST NOT use early data (i.e., 0-RTT) because POST is
not idempotent.

> Clients are not required to support any type of HTTP
authentication ({{Section 11 of HTTP}}) nor Cookies {{COOKIES}}. Thus, servers
can not rely on these features to be available.

> Clients and servers are expected to follow other rules and
restrictions in {{HTTP}}.  Note that some of those rules are for
HTTP methods other than POST; clearly, only the rules that apply
to POST are relevant for this specification.

## PKI Request

A PKI Request using the POST method is constructed as follows:

The Content-Type field MUST have the appropriate value from {{mime-id}}.

A Content-Type field for a request:

> Content-Type: application/pkcs7-mime; smime-type=CMC-Request; name=request.p7m

The content of the message is the binary value of the encoding of the
PKI Request.

##  PKI Response

The content of an HTTP-based PKI Response is
the binary value of the BER (Basic Encoding
Rules) encoding {{X690}} of either a Simple or Full PKI Response.

The Content-Type field MUST have the appropriate value from {{mime-id}}.

A Content-Type field for a response:

> Content-Type: application/pkcs7-mime; smime-type=CMC-Response; name=response.p7m

# TCP-Based Protocol

When CMC messages are sent over a TCP-based connection, no wrapping
is required of the message.  Messages are sent in their binary
encoded form.

The client closes a connection after receiving a response, or it
issues another request to the server using the same connection.
Reusing one connection for multiple successive requests, instead of
opening multiple connections that are only used for a single request,
is RECOMMENDED for performance and resource conservation reasons.
The client MUST wait for the full response before making another request
on the same connection. A
server MAY close a connection after it has been idle for some period
of time; this timeout would typically be several minutes long.

CMC requires a registered port number to send and receive CMC
messages over TCP.  The Service Name is "pkix-cmc".
The TCP port number is 5318.

Prior to {{CMC-Updates}}, CMC did not have a registered port number and
used an externally configured port from the Private Port range.
Client implementations MAY continue to use a port chosen from the
Private Port range.  A TCP Server SHOULD use port 5318 assigned to the CMC service.
It is expected that HTTP will continue to be the primary transport method used by
CMC installations.


# IANA Considerations

IANA has assigned a TCP port number in the Registered Port Number
range for the use of CMC.

~~~
  Service name: pkix-cmc
  Port Number: 5318
  Transport protocol: TCP
  Description: PKIX Certificate Management using CMS (CMC)
  Reference: [RFC-to-be]
  Assignee: iesg@ietf.org
  Contact: chair@ietf.org
~~~

IANA is requested to update the existing references to {{CMC-TRANSv1}} in the
Media Type Sub-Parameter Registries for CMC-Request and CMC-Response to [ RFC-to-be ].

# Security Considerations

Mechanisms for thwarting replay attacks may be required in particular
implementations of this protocol depending on the operational
environment.  In cases where the Certification Authority (CA)
maintains significant state information, replay attacks may be
detectable without the inclusion of the (optional) CMC nonce mechanisms.
[Implementers/Designers] of this protocol need to carefully consider
environmental conditions before choosing whether or not to [implement/use]
the senderNonce and recipientNonce attributes described in
{{Section 6.6 of CMC-STRUCT}}.  Developers of state-constrained PKI clients are
strongly encouraged to incorporate the use of these attributes.

Initiation of a secure communications channel between an end-entity
and a CA or Registration Authority (RA) -- and, similarly, between an
RA and another RA or CA -- necessarily requires an out-of-band trust
initiation mechanism.  For example, a secure channel may be
constructed between the end-entity and the CA via IPsec {{IPsec}} or
TLS {{TLS}}.  Many such schemes exist, and the choice of any particular
scheme for trust initiation is outside the scope of this document.
Implementers of this protocol are strongly encouraged to consider
generally accepted principles of secure key management when
integrating this capability within an overall security architecture.

In some instances, no prior out-of-band trust will have been
initiated prior to use of this protocol.  This can occur when the
protocol itself is being used to download onto the system the set of
trust anchors to be used for these protocols.  In these instances,
the Enveloped Data content type ({{Section 3.2.1.3.3 of CMC-STRUCT}})
provides the same shrouding that TLS would have provided.


--- back


# Acknowledgements
{:numbered="false"}

Obviously, the authors of this version of the document would like to
thank Jim Schaad and Michael Myers for their work on the previous
version of this document.

The acknowledgement from the previous version of this document follows:

The authors and the PKIX Working Group are grateful for the
participation of Xiaoyi Liu and Jeff Weinstein in helping to author
the original versions of this document.

The authors would like to thank Brian LaMacchia for his work in
developing and writing up many of the concepts presented in this
document.  The authors would also like to thank Alex Deacon and Barb
Fox for their contributions.

---
title: "Internet X.509 Public Key Infrastructure: Algorithm Identifiers for HSS and XMSS"
abbrev: "HSS and XMSS for X.509"
category: std
stand_alone: true # This lets us do fancy auto-generation of references
ipr: trust200902

docname: draft-ietf-lamps-x509-shbs-latest
submissiontype: IETF
v: 3
area: sec
workgroup: LAMPS - Limited Additional Mechanisms for PKIX and SMIME
keyword: Internet-Draft
venue:
  group: LAMPS
  type: Working Group
  mail: spasm@ietf.org
  arch: "https://mailarchive.ietf.org/arch/browse/spasm/"
  github: "x509-hbs/draft-x509-shbs"

author:
-
    ins: K. Bashiri
    name: Kaveh Bashiri
    org: BSI
    email: kaveh.bashiri.ietf@gmail.com
-
    ins: S. Fluhrer
    name: Scott Fluhrer
    org: Cisco Systems
    email: sfluhrer@cisco.com
-
    ins: S. Gazdag
    name: Stefan Gazdag
    org: genua GmbH
    email: ietf@gazdag.de
-
    ins: D. Van Geest
    name: Daniel Van Geest
    org: CryptoNext Security
    email: daniel.vangeest@cryptonext-security.com
-
    ins: S. Kousidis
    name: Stavros Kousidis
    org: BSI
    email: kousidis.ietf@gmail.com

normative:
  RFC5911:
  RFC5280: #v3 cer, v2 crl
  RFC8391: #xmss
  RFC8554: #hsslms
  SP800208:
    target: https://doi.org/10.6028/NIST.SP.800-208
    title: Recommendation for Stateful Hash-Based Signature Schemes
    author:
      -
        ins: National Institute of Standards and Technology (NIST)
    date: 2020-10-29

informative:
  RFC3279:
  RFC8410:
  RFC8411:
  MCGREW:
    target: https://tubiblio.ulb.tu-darmstadt.de/id/eprint/101633
    title: State Management for Hash-Based Signatures
    author:
      -
        ins: D. McGrew
      -
        ins: P. Kampanakis
      -
        ins: S. Fluhrer
      -
        ins: S. Gazdag
      -
        ins: D. Butin
      -
        ins: J. Buchmann
    date: 2016-11-02
  BH16:
    target: https://eprint.iacr.org/2016/1042.pdf
    title: Oops, I did it again – Security of One-Time Signatures under Two-Message Attacks.
    author:
      -
        ins: L. Bruinderink
      -
        ins: S. Hülsing
    date: 2016
  CNSA2.0:
    target: https://media.defense.gov/2022/Sep/07/2003071834/-1/-1/0/CSA_CNSA_2.0_ALGORITHMS_.PDF
    title: Commercial National Security Algorithm Suite 2.0 (CNSA 2.0) Cybersecurity Advisory (CSA)
    author:
      -
        ins: National Security Agency (NSA)
    date: 2022-09-07
  ETSI-TR-103-692:
    target: https://www.etsi.org/deliver/etsi_tr/103600_103699/103692/01.01.01_60/tr_103692v010101p.pdf
    title: State management for stateful authentication mechanisms
    author:
      -
        ins: European Telecommunications Standards Institute (ETSI)
    date: 2021-11
  IANA-LMS:
    target: https://www.iana.org/assignments/leighton-micali-signatures/
    title: Leighton-Micali Signatures (LMS)
    author:
      -
        ins: IANA
  IANA-XMSS:
    target: https://iana.org/assignments/xmss-extended-hash-based-signatures/
    title: "XMSS: Extended Hash-Based Signatures"
    author:
      -
        ins: IANA

--- abstract

This document specifies algorithm identifiers and ASN.1 encoding formats for
the Stateful Hash-Based Signature Schemes (S-HBS) Hierarchical Signature System
(HSS), eXtended Merkle Signature Scheme (XMSS), and XMSS^MT, a multi-tree
variant of XMSS. This specification applies to the Internet X.509 Public Key
infrastructure (PKI) when those digital signatures are used in Internet X.509
certificates and certificate revocation lists.

--- middle

# Introduction

Stateful Hash-Based Signature Schemes (S-HBS) such as HSS, XMSS and XMSS^MT
combine Merkle trees with One Time Signatures (OTS) in order to provide digital
signature schemes that remain secure even when quantum computers become
available. Their theoretic security is well understood and depends only on the
security of the underlying hash function. As such they can serve as an
important building block for quantum computer resistant information and
communication technology.

The private key of S-HBS is a finite collection of OTS keys, hence only a
limited number of messages can be signed and the private key's state must be
updated and persisted after signing to prevent reuse of OTS keys.  While the
right selection of algorithm parameters would allow a private key to sign a
virtually unbounded number of messages (e.g. 2^60), this is at the cost of a
larger signature size and longer signing time. Due to the statefulness of the
private key and the limited number of signatures that can be created, S-HBS
might not be appropriate for use in interactive protocols. However, in some use
cases the deployment of S-HBS may be appropriate. Such use cases are described
and discussed later in {{use-cases-shbs-x509}}.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Use Cases of S-HBS in X.509 {#use-cases-shbs-x509}

As many cryptographic algorithms that are considered to be quantum-resistant,
S-HBS have several pros and cons regarding their practical usage. On the
positive side they are considered to be secure against a classical as well as a
quantum adversary, and a secure instantiation of S-HBS may always be built as
long as a cryptographically secure hash function exists. Moreover, S-HBS offer
small public key sizes, and, in comparison to other post-quantum signature
schemes, the S-HBS can offer relatively small signature sizes (for certain
parameter sets). While key generation and signature generation may take longer
than classical alternatives, fast and minimal verification routines can be
built.  The major negative aspect is the statefulness.  Private keys always
have to be handled in a secure manner, S-HBS necessitate a special treatment of
the private key in order to avoid security incidents like signature forgery
[MCGREW], [SP800208]. Therefore, for S-HBS, a secure environment MUST be used
for key generation and key management.

Note that, in general, root CAs offer such a secure environment and the number
of issued signatures (including signed certificates and CRLs) is often moderate
due to the fact that many root CAs delegate OCSP services or the signing of
end-entity certificates to other entities (such as subordinate CAs) that use
stateless signature schemes. Therefore, many root CAs should be able to handle
the required state management, and S-HBS offer a viable solution.

As the above reasoning for root CAs usually does not apply for subordinate CAs,
it is NOT RECOMMENDED for subordinate CAs to use S-HBS for issuing end-entity
certificates. Moreover, S-HBS MUST NOT be used for end-entity certificates.

However, S-HBS MAY be used for code signing certificates, since they are
suitable and recommended in such non-interactive contexts. For example, see the
recommendations for software and firmware signing in [CNSA2.0]. Some
manufactures use common and well-established key formats like X.509 for their
code signing and update mechanisms. Also there are multi-party IoT ecosystems
where publicly trusted code signing certificates are useful.

# Algorithm Identifiers and Parameters

In this document, we define new OIDs for identifying the different stateful
hash-based signature algorithms. An additional OID is defined in {{!I-D.draft-ietf-lamps-rfc8708bis}} and
repeated here for convenience. For all of the OIDs, the parameters MUST be
absent.

## HSS Algorithm Identifier

The object identifier and public key algorithm identifier for HSS is defined in
{{!I-D.draft-ietf-lamps-rfc8708bis}}. The definitions are repeated here for reference.

The object identifier for an HSS public key is `id-alg-hss-lms-hashsig`:

    id-alg-hss-lms-hashsig  OBJECT IDENTIFIER ::= {
       iso(1) member-body(2) us(840) rsadsi(113549) pkcs(1) pkcs9(9)
       smime(16) alg(3) 17 }

Note that the `id-alg-hss-lms-hashsig` algorithm identifier is also referred to
as `id-alg-mts-hashsig`. This synonym is based on the terminology used in an
early draft of the document that became [RFC8554].

The public key and signature values identify the hash function and the height used in the
HSS/LMS tree. [RFC8554] and [SP800208] define these values, but an IANA registry
[IANA-LMS] permits the registration of additional identifiers in the future.

## XMSS Algorithm Identifier

The object identifier for an XMSS public key is `id-alg-xmss-hashsig`:

    id-alg-xmss-hashsig  OBJECT IDENTIFIER ::= {
       TBD }

The public key and signature values identify the hash function and the height used in the
XMSS tree. [RFC8391] and [SP800208] define these values, but an IANA registry
[IANA-XMSS] permits the registration of additional identifiers in the future.

## XMSS^MT Algorithm Identifier

The object identifier for an XMSS^MT public key is `id-alg-xmssmt-hashsig`:

    id-alg-xmssmt-hashsig  OBJECT IDENTIFIER ::= {
       TBD }

The public key and signature values identify the hash function and the height used in the
XMSS^MT tree. [RFC8391] and [SP800208] define these values, but an IANA registry
[IANA-XMSS] permits the registration of additional identifiers in the future.

# Public Key Identifiers

Certificates conforming to [RFC5280] can convey a public key for any public key
algorithm. The certificate indicates the algorithm through an algorithm
identifier. An algorithm identifier consists of an OID and optional parameters.

[RFC8554] and [RFC8391] define the raw octet string encodings of the public
keys used in this document. When used in a SubjectPublicKeyInfo type, the
subjectPublicKey BIT STRING contains the raw octet string encodings of the
public keys.

This document defines ASN.1 OCTET STRING types for encoding the public keys
when not used in a SubjectPublicKeyInfo. The OCTET STRING is mapped to a
subjectPublicKey (a value of type BIT STRING) as follows: the most significant
bit of the OCTET STRING value becomes the most significant bit of the BIT
STRING value, and so on; the least significant bit of the OCTET STRING
becomes the least significant bit of the BIT STRING.

## HSS Public Keys

The HSS public key identifier is as follows:

    pk-HSS-LMS-HashSig PUBLIC-KEY ::= {
       IDENTIFIER id-alg-hss-lms-hashsig
       -- KEY no ASN.1 wrapping --
       PARAMS ARE absent
       CERT-KEY-USAGE
          { digitalSignature, nonRepudiation, keyCertSign, cRLSign } }

The HSS public key is defined as follows:

    HSS-LMS-HashSig-PublicKey ::= OCTET STRING

[RFC8554] defines the raw octet string encoding of an HSS public key using the
`hss_public_key` structure. See [SP800208] and [RFC8554] for more information on
the contents and format of an HSS public key. Note that the single-tree signature
scheme LMS is instantiated as HSS with number of levels being equal to 1.

##  XMSS Public Keys

The XMSS public key identifier is as follows:

    pk-XMSS-HashSig PUBLIC-KEY ::= {
       IDENTIFIER id-alg-xmss-hashsig
       -- KEY no ASN.1 wrapping --
       PARAMS ARE absent
       CERT-KEY-USAGE
          { digitalSignature, nonRepudiation, keyCertSign, cRLSign } }

The XMSS public key is defined as follows:

    XMSS-HashSig-PublicKey ::= OCTET STRING

[RFC8391] defines the raw octet string encoding of an HSS public key using the
`xmss_public_key` structure. See [SP800208] and [RFC8391] for more information
on the contents and format of an XMSS public key.

## XMSS^MT Public Keys

The XMSS^MT public key identifier is as follows:

    pk-XMSSMT-HashSig PUBLIC-KEY ::= {
       IDENTIFIER id-alg-xmssmt-hashsig
       -- KEY no ASN.1 wrapping --
       PARAMS ARE absent
       CERT-KEY-USAGE
          { digitalSignature, nonRepudiation, keyCertSign, cRLSign } }

The XMSS^MT public key is defined as follows:

    XMSSMT-HashSig-PublicKey ::= OCTET STRING

[RFC8391] defines the raw octet string encoding of an HSS public key using the
`xmssmt_public_key` structure. See [SP800208] and [RFC8391] for more information
on the contents and format of an XMSS^MT public key.

# Key Usage Bits

The intended application for the key is indicated in the keyUsage certificate
extension [RFC5280].
When one of the AlgorithmIdentifiers specified in this document appears in the SubjectPublicKeyInfo
field of a certification authority (CA) X.509 certificate [RFC5280], the
certificate key usage extension MUST contain at least one of the
following values: digitalSignature, nonRepudiation, keyCertSign, or
cRLSign. However, it MUST NOT contain other values.

When one of these AlgorithmIdentifiers appears in the SubjectPublicKeyInfo
field of an end entity X.509 certificate [RFC5280], the certificate key usage
extension MUST contain at least one of the following values: digitalSignature
or nonRepudiation. However, it MUST NOT contain other values.

Note that for certificates that indicate `id-alg-hss-lms-hashsig` the above
definitions are more restrictive than the requirement defined in Section 4 of
{{!I-D.draft-ietf-lamps-rfc8708bis}}.

# Signature Algorithms

This section identifies OIDs for signing using HSS, XMSS, and XMSS^MT. When
these algorithm identifiers appear in the algorithm field as an
AlgorithmIdentifier, the encoding MUST omit the parameters field. That is, the
AlgorithmIdentifier SHALL be a SEQUENCE of one component, one of the OIDs
defined in the following subsections.

When the signature algorithm identifiers described in this document are used to
create a signature on a message, no digest algorithm is applied to the message
before signing.  That is, the full data to be signed is signed rather than
a digest of the data.

For HSS, the signature value is described in section 6.4 of [RFC8554]. For XMSS
and XMSS^MT the signature values are described in sections B.2 and C.2 of
[RFC8391], respectively. The octet string representing the signature is encoded
directly in the OCTET STRING without adding any additional ASN.1 wrapping. For
the Certificate and CertificateList structures, the signature value is wrapped
in the "signatureValue" OCTET STRING field.

## HSS Signature Algorithm

The HSS public key OID is also used to specify that an HSS signature was
generated on the full message, i.e. the message was not hashed before being
processed by the HSS signature algorithm.

    id-alg-hss-lms-hashsig OBJECT IDENTIFIER ::= {
       iso(1) member-body(2) us(840) rsadsi(113549) pkcs(1) pkcs9(9)
       smime(16) alg(3) 17 }

The HSS signature is defined as follows:

    HSS-LMS-HashSig-Signature ::= OCTET STRING

See [SP800208] and [RFC8554] for more information on the contents and
format of an HSS signature.

## XMSS Signature Algorithm

The XMSS public key OID is also used to specify that an XMSS signature was
generated on the full message, i.e. the message was not hashed before being
processed by the XMSS signature algorithm.

    id-alg-xmss-hashsig  OBJECT IDENTIFIER ::= {
       TBD }

The XMSS signature is defined as follows:

    XMSS-HashSig-Signature ::= OCTET STRING

See [SP800208] and [RFC8391] for more information on the contents and
format of an XMSS signature.

The signature generation MUST be performed according to 7.2 of
[SP800208].

## XMSS^MT Signature Algorithm

The XMSS^MT public key OID is also used to specify that an XMSS^MT signature
was generated on the full message, i.e. the message was not hashed before being
processed by the XMSS^MT signature algorithm.

    id-alg-xmssmt-hashsig  OBJECT IDENTIFIER ::= {
       TBD }

The XMSS^MT signature is defined as follows:

    XMSSMT-HashSig-Signature ::= OCTET STRING

See [SP800208] and [RFC8391] for more information on the contents and
format of an XMSS^MT signature.

The signature generation MUST be performed according to 7.2 of
[SP800208].

# Key Generation

The key generation for XMSS and XMSS^MT MUST be performed according to 7.2 of
[SP800208]

# ASN.1 Module {#sec-asn1}

For reference purposes, the ASN.1 syntax is presented as an ASN.1 module here.
This ASN.1 Module builds upon the conventions established in [RFC5911].

~~~
{::include X509-SHBS-2024.asn}
~~~

# Security Considerations

The security requirements of [SP800208] MUST be taken into account.

For S-HBS it is crucial to stress the importance of a correct state management.
If an attacker were able to obtain signatures for two different messages
created using the same OTS key, then it would become computationally feasible
for that attacker to create forgeries [BH16]. As noted in [MCGREW] and
[ETSI-TR-103-692], extreme care needs to be taken in order to avoid the risk
that an OTS key will be reused accidentally.  This is a new requirement that
most developers will not be familiar with and requires careful handling.

Various strategies for a correct state management can be applied:

- Implement a track record of all signatures generated by a key pair associated
  to a S-HBS instance. This track record may be stored outside the
  device which is used to generate the signature. Check the track record to
  prevent OTS key reuse before a new signature is released. Drop the new
  signature and hit your PANIC button if you spot OTS key reuse.

- Use a S-HBS instance only for a moderate number of signatures such
  that it is always practical to keep a consistent track record and be able to
  unambiguously trace back all generated signatures.

- Apply the state reservation strategy described in Section 5 of [MCGREW], where
  upcoming states are reserved in advance by the signer. In this way the number of
  state synchronisations between nonvolatile and volatile memory is reduced.


# Backup and Restore Management

Certificate Authorities have high demands in order to ensure the availability
of signature generation throughout the validity period of signing key pairs.

Usual backup and restore strategies when using a stateless signature scheme
(e.g. SLH-DSA) are to duplicate private keying material and to operate
redundant signing devices or to store and safeguard a copy of the private
keying material such that it can be used to set up a new signing device in case
of technical difficulties.

For S-HBS such straightforward backup and restore strategies will lead to OTS
reuse with high probability as a correct state management is not guaranteed.
Strategies for maintaining availability and keeping a correct state are
described in Section 7 of [SP800208].

# IANA Considerations

IANA is requested to assign a module OID from the "SMI for PKIX Module
Identifier" registry for the ASN.1 module in {{sec-asn1}}.

--- back

# Acknowledgments
{:numbered="false"}

Thanks for Russ Housley and Panos Kampanakis for helpful suggestions.

This document uses a lot of text from similar documents [SP800208],
([RFC3279] and [RFC8410]) as well as {{!I-D.draft-ietf-lamps-rfc8708bis}}. Thanks go to the authors of
those documents. "Copying always makes things easier and less error prone" -
[RFC8411].

---
title: "Post-Quantum Key Encapsulation Mechanisms (PQ KEMs) for COSE"
abbrev: "PQ KEM for COSE"
category: std

docname: draft-ietf-cose-pqc-kem-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Security"
workgroup: "COSE"
keyword:
 - PQC
 - COSE
 - Hybrid

venue:
  group: "cose"
  type: "Working Group"
  mail: "cose@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/cose/"
  github: "cose-wg/draft-ietf-cose-pqc-kem"

stand_alone: yes
pi: [toc, sortrefs, symrefs, strict, comments, docmapping]

author:
 -
    fullname: Tirumaleswar Reddy
    organization: Nokia
    city: Bangalore
    region: Karnataka
    country: India
    email: "kondtir@gmail.com"
 -
    fullname: Aritra Banerjee
    organization: Nokia
    city: London
    country: United Kingdom
    email: "aritra.banerjee@nokia.com"
 -
    ins: H. Tschofenig
    fullname: Hannes Tschofenig
    organization: University of the Bundeswehr Munich
    abbrev: UniBw M.
    city: Neubiberg
    region: Bavaria
    country: Germany
    code: 85577
    email: hannes.tschofenig@gmx.net

normative:
  RFC2119:
  RFC8174:
  RFC8949:
  RFC9052:
  RFC9053:
  COSE-IANA:
     author:
        org: IANA
     title: CBOR Object Signing and Encryption (COSE)
     target: https://www.iana.org/assignments/cose

informative:

  PQCAPI:
     title: "PQC - API notes"
     target: https://csrc.nist.gov/CSRC/media/Projects/Post-Quantum-Cryptography/documents/example-files/api-notes.pdf
     date: false
  FO:
     title: "Secure Integration of Asymmetric and Symmetric Encryption Schemes"
     target: https://link.springer.com/article/10.1007/s00145-011-9114-1
     date: false
  HHK:
     title: "A Modular Analysis of the Fujisaki-Okamoto Transformation"
     target: https://link.springer.com/chapter/10.1007/978-3-319-70500-2_12
     date: false
  FIPS203:
     title: "FIPS-203: Module-Lattice-based Key-Encapsulation Mechanism Standard"
     target: https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.203.pdf
     date: false
  SP-800-108r1:
     title: "Recommendation for Key Derivation Using Pseudorandom Functions"
     target: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-108r1-upd1.pdf
     date: false
  NISTFINAL:
    title: "NIST Releases First 3 Finalized Post-Quantum Encryption Standards"
    target: https://www.nist.gov/news-events/news/2024/08/nist-releases-first-3-finalized-post-quantum-encryption-standards
  RSA:
     title: "A Method for Obtaining Digital Signatures and Public-Key Cryptosystems+"
     target: https://dl.acm.org/doi/pdf/10.1145/359340.359342
     date: false
  NIST.SP.800-56Ar3:
     author:
        org: National Institute of Standards and Technology
     title: Recommendation for Pair-Wise Key-Establishment Schemes Using Discrete Logarithm Cryptography, NIST Special Publication 800-56A Revision 3
     target: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-56Ar3.pdf
     date: April 2018

--- abstract

This document describes conventions for using Post-Quantum Key Encapsulation
Mechanisms (PQ-KEMs) with CBOR Object Signing and Encryption (COSE). It
specifies the use of ML-KEM and of PQ/T hybrid KEMs that combine ML-KEM with a
traditional elliptic curve algorithm.

--- middle

# Introduction

Quantum computing is no longer perceived as a consequence of computational sciences and theoretical physics.  Considerable research efforts and enormous corporate and government funding for the development of practical quantum computing systems are being invested currently. As such, as quantum technology advances, there is the potential for future quantum computers to have a significant impact on current cryptographic systems.

Researchers have developed Post-Quantum Key Encapsulation Mechanisms (PQ-KEMs) to provide secure key establishment resistant against an adversary with access to a quantum computer.

The National Institute of Standards and Technology (NIST) has standardized
ML-KEM as a post-quantum key encapsulation mechanism in {{FIPS203}}. This
document specifies how ML-KEM is used to protect the confidentiality of content
encrypted with COSE against adversaries with access to quantum computers.

Although this mechanism could thus be used with any PQ-KEM, this document focuses on Module-Lattice-based Key Encapsulation Mechanisms (ML-KEMs). ML-KEM is a one-pass (store-and-forward) cryptographic mechanism for an originator to securely send keying material to a recipient
using the recipient's ML-KEM public key. Three parameters sets for ML-KEMs are specified by {{FIPS203}}. In order of increasing security strength (and decreasing performance), these parameter sets
are ML-KEM-512, ML-KEM-768, and ML-KEM-1024.

Some deployments want to retain the security of a traditional algorithm while
migrating to post-quantum cryptography, so that confidentiality is preserved
as long as at least one of the two algorithms remains secure. For these
deployments, this document also specifies the use of the PQ/T hybrid KEMs
defined in {{!I-D.irtf-cfrg-concrete-hybrid-kems}}, which combine ML-KEM with
an elliptic curve group. A PQ/T hybrid KEM exposes the same interface as
ML-KEM and is used in COSE in exactly the same way. Only the algorithm
identifiers and the sizes of keys and ciphertexts differ.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

This document makes use of the terms defined in {{?I-D.ietf-pquip-pqt-hybrid-terminology}}. The following terms are repeatedly used in this specification:

- KEM: Key Encapsulation Mechanism
- PQ-KEM: Post-Quantum Key Encapsulation Mechanism
- CEK: Content Encryption Key
- ML-KEM: Module-Lattice-based Key Encapsulation Mechanism
- PQ/T Hybrid KEM: A KEM that combines a post-quantum and a traditional
  component algorithm into a single KEM

Unless stated otherwise, the term "PQ-KEM" in this document covers both ML-KEM
and the PQ/T hybrid KEMs specified in {{hybrid-kems}}.

For the purposes of this document, it is helpful to be able to divide cryptographic algorithms into two classes:

"Traditional Algorithm": An asymmetric cryptographic algorithm based on integer
factorization, finite-field discrete logarithms, or elliptic-curve discrete
logarithms. In the context of COSE, examples of traditional key exchange
algorithms include Ephemeral-Static (ES) DH and Static-Static (SS) DH
{{RFC9052}}.

"Post-Quantum Algorithm":  An asymmetric cryptographic algorithm that is believed to be secure against attacks using quantum computers as well as classical computers. Post-quantum algorithms can also be called quantum-resistant or quantum-safe algorithms. Examples of Post-Quantum Algorithm include ML-KEM.

## Key Encapsulation Mechanisms {#KEMs}

For the purposes of this document, we consider a Key Encapsulation Mechanism (KEM) to be any asymmetric cryptographic scheme comprised of algorithms satisfying the following interfaces {{PQCAPI}}.

* def kemKeyGen() -> (pk, sk)
* def kemEncaps(pk) -> (ct, ss)
* def kemDecaps(ct, sk) -> ss

where pk is public key, sk is secret key, ct is the ciphertext representing an encapsulated key, and ss is shared secret.

This document uses the COSE header parameter `ek` to carry the KEM ciphertext
`ct` produced by PQ-KEM encapsulation. This differs from the terminology used
in FIPS 203, {{?RFC9936}}, and {{I-D.irtf-cfrg-concrete-hybrid-kems}}, where
`ek` denotes the public encapsulation key. In this document, the public
encapsulation key is represented as the public component of an AKP key, while
the COSE header parameter `ek` carries the encapsulated key, namely the KEM
ciphertext `ct`.

KEMs are typically used in cases where two parties, hereby refereed to as the "encapsulater" and the "decapsulater", wish to establish a shared secret via public key cryptography, where the decapsulater has an asymmetric key pair and has previously shared the public key with the encapsulater.

# Design Rationales {#rational}

Sections 8.5.4 and 8.5.5 of COSE {{RFC9052}} define Direct Key Agreement and
Key Agreement with Key Wrap, respectively. This document specifies the use of
PQ-KEMs in these two modes. In Direct Key Agreement mode, the derived shared
secret is used as the content encryption key (CEK). In Key Agreement with Key
Wrap mode, the derived shared secret is used as a key-encryption key to wrap
the CEK.

Key Agreement with Key Wrap supports efficient encryption for multiple
recipients: the content is encrypted once with the CEK, and an individually
wrapped CEK is provided for each recipient.

ML-KEM is IND-CCA2 secure, as stated in Section 3.2 of {{FIPS203}}. This
property comes from a variant of the Fujisaki-Okamoto transform ({{FO}},
{{HHK}}) that is built into the ML-KEM encapsulation and decapsulation
algorithms. A COSE implementation does not need to add anything to obtain it.
The PQ/T hybrid KEMs specified in {{hybrid-kems}} are IND-CCA2 secure as well.
Because of this property, a recipient's PQ-KEM public key can be reused for
many encapsulations.

The KDF specified in {{kdf}} does not contribute to the IND-CCA2 security of
the PQ-KEM. It is applied to the PQ-KEM shared secret to derive a key of the
length required by the content encryption or key wrap algorithm and to bind
that key to the COSE context, including the algorithm identifiers.

During the transition from traditional to post-quantum algorithms, some
deployments combine both types of algorithms. {{hybrid-kems}} specifies the
use of PQ/T hybrid KEMs for this purpose. A PQ/T hybrid KEM is treated as a
single KEM with a single public key and a single ciphertext. This document
does not define a way to combine separately conveyed traditional and
post-quantum recipient structures. The use of PQ-KEMs with HPKE in COSE is
specified in {{?I-D.ietf-cose-hpke}} and is outside the scope of this document.

# KEM PQC Algorithms

At time of writing, NIST have standardized three PQC algorithms, with more expected to be standardised in the future ({{NISTFINAL}}). These algorithms are not necessarily drop-in replacements for traditional asymmetric cryptographic algorithms. For instance, RSA {{RSA}} and ECC {{?RFC6090}} can be used as both a key encapsulation method (KEM) and as a signature scheme, whereas there is currently no post-quantum algorithm that can perform both functions.

## ML-KEM

ML-KEM offers several parameter sets with varying levels of security and performance trade-offs. This document specifies the use of the ML-KEM algorithm at three security levels: ML-KEM-512, ML-KEM-768, and ML-KEM-1024. ML-KEM key generation, encapsulation and decaspulation functions are defined in {{FIPS203}}. The main security property for KEMs standardized in the NIST Post-Quantum Cryptography Standardization Project is indistinguishability under adaptive chosen ciphertext attacks (IND-CCA2) (see Section 10.2 of {{?I-D.ietf-pquip-pqc-engineers}}). The public/private key sizes, ciphertext key size, and PQ security levels of ML-KEM are detailed in Section 12 of {{?I-D.ietf-pquip-pqc-engineers}}.

## PQ/T Hybrid KEMs {#hybrid-kems}

This document specifies the use of the three PQ/T hybrid KEMs defined in
{{I-D.irtf-cfrg-concrete-hybrid-kems}}. Each of them combines ML-KEM with an
elliptic curve group using the CG framework of
{{!I-D.irtf-cfrg-hybrid-kems}}, with SHAKE256 as the PRG and SHA3-256 as the
KDF of the combiner:

- MLKEM768-P256 combines ML-KEM-768 with P-256.
- MLKEM768-X25519 combines ML-KEM-768 with X25519. It is identical to X-Wing
  {{?I-D.connolly-cfrg-xwing-kem}}.
- MLKEM1024-P384 combines ML-KEM-1024 with P-384.

The functions GenerateKeyPair, Encaps, and Decaps of
{{I-D.irtf-cfrg-hybrid-kems}} provide the kemKeyGen, kemEncaps, and kemDecaps
functions of {{KEMs}}, respectively. The combiner that produces the hybrid
shared secret from the two component shared secrets is part of the hybrid KEM.
It is distinct from, and applied before, the KDF specified in {{kdf}}.

{{hybrid-sizes}} lists the sizes, in octets, of the values used by the hybrid
KEMs. These are the Nek, Ndk, Nct, and Nss constants of
{{I-D.irtf-cfrg-concrete-hybrid-kems}}.

| Hybrid KEM      | Public key | Private key | Ciphertext | Shared secret |
|-----------------|-----------:|------------:|-----------:|--------------:|
| MLKEM768-P256   |       1249 |          32 |       1153 |            32 |
| MLKEM768-X25519 |       1216 |          32 |       1120 |            32 |
| MLKEM1024-P384  |       1665 |          32 |       1665 |            32 |
{: #hybrid-sizes title="Sizes of PQ/T Hybrid KEM Values in Octets"}

The public key and the ciphertext of a hybrid KEM are each a single opaque
byte string. Implementations MUST NOT split them into their component parts
for conveyance in COSE, and MUST NOT use the component keys of a hybrid KEM
key pair with any other algorithm.

## PQ-KEM Encapsulation {#encrypt}

The encapsulation process is as follows:

1.  Generate an inital shared secret SS' and the associated ciphertext CT
    using the KEM encapsulation function and the recipient's public
    key recipPubKey.

~~~
          (SS', CT) = kemEncaps(recipPubKey)
~~~

2.  Derive a final shared secret SS of length SSLen bytes from
    the initial shared secret SS' using the underlying key derivation
    function:

~~~
          SS = KDF(SS', SSLen)
~~~

In Direct Key Agreement mode, the output of the KDF MUST have the key length
required by the content encryption algorithm used by the COSE_Encrypt
structure. In Key Agreement with Key Wrap mode, the output of the KDF MUST have
the key length required by the key wrap algorithm selected by the recipient
algorithm. For the algorithms defined in this specification, the KDF output
length is 128 bits for ML-KEM-512+A128KW, 192 bits for ML-KEM-768+A192KW,
MLKEM768-P256+A192KW, and MLKEM768-X25519+A192KW, and 256 bits for
ML-KEM-1024+A256KW and MLKEM1024-P384+A256KW.

When Direct Key Agreement is employed, SS is the CEK. When Key Agreement with Key Wrapping is employed, SS is the key-encryption key used with AES Key Wrap to wrap the CEK; SS is not the CEK.

## PQ-KEM Decapsulation {#decrypt}

The decapsulation process is as follows:

1.  Decapsulate the ciphertext CT using the KEM decapsulation
    function and the recipient's private key to retrieve the initial shared
    secret SS':

~~~
          SS' = kemDecaps(recipPrivKey, CT)
~~~

    If the decapsulation operation outputs an error, output "decryption error", and stop.

2.  Derive the final shared secret SS of length SSLen bytes from
    the inital secret SS' using the underlying key derivation
    function:

~~~
          SS = KDF(SS', SSLen)
~~~

# KDF {#kdf}

## Key Derivation

The key derivation for COSE is performed using KMAC as defined in NIST
SP 800-108r1-upd1 {{SP-800-108r1}}. The KMAC(K, X, L, S) parameters are
instantiated as follows:

   *  K: the input key-derivation key. In this document this is the initial shared secret (SS') outputted from the
      kemEncaps() or kemDecaps() functions.

   *  X: The context structure defined in Section 5.2 of {{RFC9053}}, excluding
      the PartyUInfo and PartyVInfo fields. PartyUInfo is omitted because sender
      authentication is not available in PQ-KEMs. PartyVInfo is omitted because
      the recipient's identity is bound to the public key used for
      encapsulation. If mutually known private information is included, the
      sender and recipient MUST agree out of band to include it as SuppPrivInfo,
      as defined in {{NIST.SP.800-56Ar3}}.

   *  L: length of the output key in bits. In Direct Key Agreement mode, L is set to the key length required by the content encryption algorithm. In Key Agreement with Key Wrapping mode, L is set to the key length required by the key wrap algorithm, as listed in {{encrypt}}.

   *  S: the optional customization label. In this document this parameter is unused, that is it is the zero-length string "".

KMAC256 is used for all security levels of ML-KEM and for all PQ/T hybrid KEMs.

# Post-Quantum KEM in COSE {#pq-kem-cose}

This specification supports two uses of PQ-KEM in COSE, namely

*  PQ-KEM in a Direct Key Agreement mode.

*  PQ-KEM in a Key Agreement with Key Wrap mode.

In both modes, the content encryption algorithm of the COSE_Encrypt
structure MUST be an AEAD algorithm.
In both modes, the COSE header parameter `ek`, defined in Section 7.2 of
{{?I-D.ietf-cose-hpke}}, is used to convey the ciphertext `ct` output by the
PQ-KEM encapsulation algorithm.

## Direct Key Agreement

The CEK will be generated using the process explained in {{encrypt}}.
Subsequently, the plaintext will be encrypted using the CEK. The resulting
ciphertext is either included in the COSE_Encrypt or is detached. If a payload is
transported separately then it is called "detached content". A nil CBOR
object is placed in the location of the ciphertext. See Section 5
of {{RFC9052}} for a description of detached payloads.

The COSE_Recipient structure for the recipient is organized as follows:

   * The sender MUST set the 'alg' parameter to indicate the use of the PQ-KEM algorithm.
   * This document RECOMMENDS the use of the 'kid' parameter
     (or other parameters) to explicitly identify the recipient public key
     used by the sender. If the COSE_Encrypt contains the 'kid' then the recipient may
     use it to select the appropriate private key.

## Key Agreement with Key Wrap

With the two layer structure the PQ-KEM information is conveyed in the COSE_recipient
structure, i.e. one COSE_recipient structure per recipient.

In this approach the following layers are involved:

- Layer 0 (corresponding to the COSE_Encrypt structure) contains the content (plaintext)
encrypted with the CEK. This ciphertext may be detached, and if not detached, then
it is included in the COSE_Encrypt structure.

- Layer 1 (corresponding to a recipient structure) contains parameters needed for
PQ-KEM to generate a shared secret used to encrypt the CEK. This layer conveys
the encrypted CEK in the "ciphertext" field (Section 5.1 of {{RFC9052}}).
The unprotected header MAY contain the kid parameter to identify the static recipient
public key the sender has been using with PQ-KEM.

This two-layer structure is used to encrypt content that can also be shared with
multiple parties at the expense of a single additional encryption operation.
As stated above, the specification uses a CEK to encrypt the content at layer 0.

# COSE Ciphersuite Registration {#COSE-PQ-KEM}

All security levels of ML-KEM internally use SHA3-256, SHA3-512, SHAKE128,
and SHAKE256. This internal usage influences the selection of the KDF described
in this document.

ML-KEM-512 MUST be used with a KDF capable of producing a key with at least
128 bits of security and, in Key Agreement with Key Wrap mode, with a key wrap
algorithm having a key length of at least 128 bits.

ML-KEM-768 MUST be used with a KDF capable of producing a key with at least
192 bits of security and, in Key Agreement with Key Wrap mode, with a key wrap
algorithm having a key length of at least 192 bits.

ML-KEM-1024 MUST be used with a KDF capable of producing a key with at least
256 bits of security and, in Key Agreement with Key Wrap mode, with a key wrap
algorithm having a key length of at least 256 bits.

The key wrap algorithm paired with a PQ/T hybrid KEM follows that of its
ML-KEM component. MLKEM768-P256 and MLKEM768-X25519 are subject to the
requirements stated above for ML-KEM-768, and MLKEM1024-P384 is subject to the
requirements stated above for ML-KEM-1024.

{{ciphersuite-table}} lists the COSE algorithm values for the PQ-KEM
ciphersuites defined by this document.

~~~
+===============================+=========+===================================+=============+
| Name                          | COSE ID | Description                       | Recommended |
+===============================+=========+===================================+=============+
| ML-KEM-512                    | TBD1    | ML-KEM-512                        | No          |
+-------------------------------+---------+-----------------------------------+-------------+
| ML-KEM-768                    | TBD2    | ML-KEM-768                        | No          |
+-------------------------------+---------+-----------------------------------+-------------+
| ML-KEM-1024                   | TBD3    | ML-KEM-1024                       | No          |
+-------------------------------+---------+-----------------------------------+-------------+
| ML-KEM-512+A128KW             | TBD4    | ML-KEM-512 + AES128KW             | No          |
+-------------------------------+---------+-----------------------------------+-------------+
| ML-KEM-768+A192KW             | TBD5    | ML-KEM-768 + AES192KW             | No          |
+-------------------------------+---------+-----------------------------------+-------------+
| ML-KEM-1024+A256KW            | TBD6    | ML-KEM-1024 + AES256KW            | No          |
+-------------------------------+---------+-----------------------------------+-------------+
| MLKEM768-P256                 | TBD7    | MLKEM768-P256                     | No          |
+-------------------------------+---------+-----------------------------------+-------------+
| MLKEM768-X25519               | TBD8    | MLKEM768-X25519                   | No          |
+-------------------------------+---------+-----------------------------------+-------------+
| MLKEM1024-P384                | TBD9    | MLKEM1024-P384                    | No          |
+-------------------------------+---------+-----------------------------------+-------------+
| MLKEM768-P256+A192KW          | TBD10   | MLKEM768-P256 + AES192KW          | No          |
+-------------------------------+---------+-----------------------------------+-------------+
| MLKEM768-X25519+A192KW        | TBD11   | MLKEM768-X25519 + AES192KW        | No          |
+-------------------------------+---------+-----------------------------------+-------------+
| MLKEM1024-P384+A256KW         | TBD12   | MLKEM1024-P384 + AES256KW         | No          |
+-------------------------------+---------+-----------------------------------+-------------+
~~~
{: #ciphersuite-table title="COSE PQ-KEM Ciphersuites."}

# Use of AKP Key Type for PQ-KEM Keys in COSE {#akp}

The "AKP" (Algorithm Key Pair) key type, defined in
{{?I-D.ietf-cose-dilithium}}, is the only key representation defined by this
document for PQ-KEM keys. A COSE_Key with "kty" set to "AKP" represents a
PQ-KEM key pair. The public key is carried in the "pub" parameter. If
included, the private key is carried in the "priv" parameter. Both parameters
are byte strings containing the raw algorithm-specific key material.

PQ-KEM keys are not represented using the "OKP" or "EC2" key types, and this
document does not register values in the "COSE Elliptic Curves" registry. The
PQ-KEM is identified by the "alg" parameter of the AKP key and not by a "crv"
parameter.

The "AKP" key type mandates the use of the "alg" parameter. The value of "alg"
MUST be one of the algorithms listed in {{ciphersuite-table}}, and a key MUST
only be used with the algorithm identified by its "alg" parameter. A key is
therefore bound either to Direct Key Agreement or to Key Agreement with Key
Wrap. A recipient that supports both modes for the same PQ-KEM publishes two
COSE_Key objects, one for each algorithm.

Note: Binding a KEM key to one of the two modes differs from the "OKP" usage
model and requires further discussion within the WG.

## ML-KEM Keys

For ML-KEM, the "pub" parameter contains the encapsulation key as specified in
{{FIPS203}}.

For ML-KEM algorithms, as specified in {{FIPS203}}, there are two possible
representations of a private key: a seed and a fully expanded private key
derived from the seed. This document specifies only the seed form. The "priv"
parameter MUST contain the 64-octet ML-KEM seed `d || z`, where `d` is the
first 32 octets and `z` is the last 32 octets. The ML-KEM public key and
expanded private key are derived from this seed using
`ML-KEM.KeyGen_internal(d, z)`, as specified in {{FIPS203}}. This document does
not define a 32-octet private seed representation and does not support carrying
the expanded private key in "priv". This avoids implicit,
implementation-specific key expansion.

## PQ/T Hybrid KEM Keys

For the PQ/T hybrid KEMs of {{hybrid-kems}}, the "pub" parameter contains the
hybrid encapsulation key and the "priv" parameter contains the hybrid
decapsulation key, both as specified in
{{I-D.irtf-cfrg-concrete-hybrid-kems}}. Their lengths are given in
{{hybrid-sizes}}.

The hybrid decapsulation key is a 32-octet seed from which the decapsulation
keys of both components are derived. The "priv" parameter MUST contain this
seed. This document does not support carrying the expanded component
decapsulation keys in "priv". A recipient MUST reject a hybrid KEM key whose
"pub" or "priv" parameter does not have the length given in {{hybrid-sizes}}
for the algorithm identified by "alg".

# Security Considerations

PQC KEMs used in the manner described in this document MUST explicitly be designed to be secure in the event that the public key is reused, such as achieving IND-CCA2 security. ML-KEM has such security properties.

ML-KEM key generation and encapsulation both rely on high-quality random input.
For key generation, the 64-octet seed `d || z` contains 32 octets used to
deterministically derive the key pair and 32 octets used as a rejection value.
For encapsulation, ML-KEM uses fresh random input to produce the ciphertext and
shared secret. Implementations MUST use a cryptographically secure random number
generator for these values. Weak or repeated random input can make it
substantially easier for an attacker to reproduce keys or ciphertexts and can
undermine the security properties expected from ML-KEM.

ML-KEM encapsulation and decapsulation output only the shared secret and
ciphertext values described by this specification. Implementations MUST NOT use
intermediate ML-KEM values directly as COSE keying material, KDF input,
authentication input, or application data. Implementations SHOULD avoid exposing
intermediate values through APIs, logs, errors, or side channels.

## PQ/T Hybrid KEMs

The PQ/T hybrid KEMs specified in {{hybrid-kems}} are designed to remain secure
as long as either the ML-KEM component or the traditional component remains
secure. The security considerations of {{I-D.irtf-cfrg-hybrid-kems}} and
{{I-D.irtf-cfrg-concrete-hybrid-kems}} apply.

A hybrid KEM does not provide more protection against a quantum-capable
attacker than its ML-KEM component. The traditional component provides a
safeguard against a failure of ML-KEM or of its implementation. It does not
raise the post-quantum security level.

The hybrid shared secret is computed by the combiner defined in
{{I-D.irtf-cfrg-hybrid-kems}}. Implementations MUST NOT replace this combiner
with the KDF specified in {{kdf}}, and MUST NOT use the shared secret of
either component on its own as COSE keying material or as KDF input.

The component keys of a hybrid KEM key pair are derived from a single seed.
Using one of these component keys with a standalone algorithm, for example
using the ML-KEM-768 component key of an MLKEM768-X25519 key with the
ML-KEM-768 algorithm of this document, removes the protection offered by the
hybrid construction and is prohibited by {{hybrid-kems}}.

## Key Wrap and Algorithm Substitution

In Key Agreement with Key Wrap mode, the CEK is not bound to the content
encryption algorithm of the COSE_Encrypt structure. {{?RFC9709}}
describes an attack on CMS that exploits this: an attacker changes the
content encryption algorithm from an AEAD algorithm to AES-CBC and, if
the recipient reveals the output of the failed decryption, for example in
an error message, recovers plaintext blocks.
The same attack applies to COSE with AES-CBC or AES-CTR {{?RFC9459}}.
It is not specific to PQ-KEMs. Direct Key Agreement is not affected,
since the content encryption algorithm is input to the KDF.

For CMS, {{?RFC9709}} mitigates the attack by encrypting the content
with a key derived from the CEK and the content encryption algorithm
identifier. A changed algorithm identifier results in a different key,
and decryption fails. This document instead requires AEAD content
encryption algorithms ({{pq-kem-cose}}). A substituted AEAD algorithm
fails authentication.

# IANA Considerations {#IANA}

## COSE Algorithms Registrations

IANA is requested to add the following entries to the "COSE Algorithms"
registry {{COSE-IANA}}:

- Name: ML-KEM-512
- Value: TBD1
- Description: PQ-KEM that uses ML-KEM-512 PQ-KEM.
- Capabilities: [kty]
- Change Controller: IESG
- Reference: This document (TBD)
- Recommended: No

- Name: ML-KEM-768
- Value: TBD2
- Description: PQ-KEM that uses ML-KEM-768 PQ-KEM.
- Capabilities: [kty]
- Change Controller: IESG
- Reference: This document (TBD)
- Recommended: No

- Name: ML-KEM-1024
- Value: TBD3
- Description: PQ-KEM that uses ML-KEM-1024 PQ-KEM.
- Capabilities: [kty]
- Change Controller: IESG
- Reference: This document (TBD)
- Recommended: No

- Name: ML-KEM-512+A128KW
- Value: TBD4
- Description: PQ-KEM that uses ML-KEM-512 PQ-KEM and CEK wrapped with "A128KW".
- Capabilities: [kty]
- Change Controller: IESG
- Reference: This document (TBD)
- Recommended: No

- Name: ML-KEM-768+A192KW
- Value: TBD5
- Description: PQ-KEM that uses ML-KEM-768 and CEK wrapped with "A192KW".
- Capabilities: [kty]
- Change Controller: IESG
- Reference: This document (TBD)
- Recommended: No

- Name: ML-KEM-1024+A256KW
- Value: TBD6
- Description: PQ-KEM that uses ML-KEM-1024 and CEK wrapped with "A256KW".
- Capabilities: [kty]
- Change Controller: IESG
- Reference: This document (TBD)
- Recommended: No

- Name: MLKEM768-P256
- Value: TBD7
- Description: PQ/T hybrid KEM that uses MLKEM768-P256.
- Capabilities: [kty]
- Change Controller: IESG
- Reference: This document (TBD)
- Recommended: No

- Name: MLKEM768-X25519
- Value: TBD8
- Description: PQ/T hybrid KEM that uses MLKEM768-X25519.
- Capabilities: [kty]
- Change Controller: IESG
- Reference: This document (TBD)
- Recommended: No

- Name: MLKEM1024-P384
- Value: TBD9
- Description: PQ/T hybrid KEM that uses MLKEM1024-P384.
- Capabilities: [kty]
- Change Controller: IESG
- Reference: This document (TBD)
- Recommended: No

- Name: MLKEM768-P256+A192KW
- Value: TBD10
- Description: PQ/T hybrid KEM that uses MLKEM768-P256 and CEK wrapped with "A192KW".
- Capabilities: [kty]
- Change Controller: IESG
- Reference: This document (TBD)
- Recommended: No

- Name: MLKEM768-X25519+A192KW
- Value: TBD11
- Description: PQ/T hybrid KEM that uses MLKEM768-X25519 and CEK wrapped with "A192KW".
- Capabilities: [kty]
- Change Controller: IESG
- Reference: This document (TBD)
- Recommended: No

- Name: MLKEM1024-P384+A256KW
- Value: TBD12
- Description: PQ/T hybrid KEM that uses MLKEM1024-P384 and CEK wrapped with "A256KW".
- Capabilities: [kty]
- Change Controller: IESG
- Reference: This document (TBD)
- Recommended: No

# Acknowledgments
{: numbered="false"}

The authors thank Mark Atwood, Ajitomi Daisuke, Brian Campbell, Daniel Huigens, Filip Skokan, Ilari Liusvaara, Neil Madden,
and Stepan Yakimovich for their contributions to this specification.

--- back

# Document History
{: numbered="false"}

[[ to be removed by the RFC Editor before publication as an RFC ]]

-01

* Added the PQ/T hybrid KEMs MLKEM768-P256, MLKEM768-X25519, and
  MLKEM1024-P384 from draft-irtf-cfrg-concrete-hybrid-kems.
* Registered six COSE algorithms for the hybrid KEMs, three for Direct Key
  Agreement and three for Key Agreement with Key Wrap.
* Defined the AKP representation of hybrid KEM keys.
* Security Considerations: added text on PQ/T hybrid KEMs.
* Corrected the description of IND-CCA2 security: ML-KEM is IND-CCA2 secure
  on its own, and the KDF does not perform the FO transform.
* Clarified that the KDF derives a key of the required length and binds it
  to the COSE context.
* Removed the statement about an upper bound on the reuse of PQ-KEM public
  keys.
* AKP is the only key representation. Removed the registrations in the
  COSE Elliptic Curves registry.
* An AKP key is bound to the algorithm in its "alg" parameter and thus to
  either Direct Key Agreement or Key Agreement with Key Wrap.
* Required AEAD content encryption algorithms.
* Security Considerations: added text on algorithm substitution with AES Key
  Wrap.

-00

* Initial COSE working group draft, based on draft-ietf-jose-pqc-kem-06.

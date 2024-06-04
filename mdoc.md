# Document Extract with mdoc

**Version:** 0.1.0-SNAPSHOT

**Authors:** [Sander Dijkhuis](mailto:sander.dijkhuis@cleverbase.com) (Cleverbase, editor)

**License:** [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)

## Introduction

The ISO/IEC 18013-5:2021 and related standards specify the mdoc as a secure document for selectively disclosed attributes. The current standards are optimised for interactive disclosure. However, online transactions often involve products for non-interactive use, delivered using trust services. These trust products include for example qualified electronic signatures and evidence of qualified electronic registered delivery. These trust products would also benefit from the security and interoperability provided by mdoc.

An example use case is the use of an mdoc representing a qualified electronic attestation of attributes as evidence of a signer role in a qualified electronic signature. Use cases always involve the trust service provider acting as an mdoc reader in an interactive retrieval process. Subsequently, the trust service provider post-processes the mdoc for non-interactive use.

The result of this post-processing we call a **Document Extract**. Security requirements to the Document Extract include:

- session encryption: no
- issuer data authentication: yes
- mdoc authentication: no
- mdoc reader authentication: no

## The Document Extract format

```cddl
DocumentExtract = {
    "version": tstr,
    "docType": DocType,            ; see ISO/IEC 18013-5:2021
    "issuerSigned": IssuerSigned,  ; see ISO/IEC 18013-5:2021
    "nameSpaces": NameSpaces
}

NameSpaces = {
    * NameSpace => Items           ; see ISO/IEC 18013-5:2021 for NameSpace
}

Items = {
    + DataElementIdentifier => DataElementValue  ; see ISO/IEC 18013-5:2021
}
```

The `version` SHALL be `"1.0"`.

## Extracting from a retrieved mdoc

Primary actor: the mdoc reader.

Prerequisite: the mdoc reader has a `Document` as retrieved per the mdoc standard.

1. Take `docType` and `issuerSigned` from the `Document`.
2. Take the `DeviceNameSpacesBytes` from the `Document` and fill into `nameSpaces`.
3. Construct the `DocumentExtract` as specified.

## Verifying the Document Extract

Primary actor: the relying party.

1. Verify that `version` is `"1.0"`. Reject otherwise.
2. Verify that the `docType` is as expected. Reject otherwise.
3. Validate the qualified electronic signature or seal in `issuerSigned`. Reject otherwise.
4. Verify the `nameSpaces` with the `issuerSigned`. Reject otherwise.
5. Accept the Document Extract.

## The Document Extract media type

> [!WARNING]
> The proposal below is not yet requested at the IANA Media Types registry.

A Document Extract can be included as a binary sequence in other trust products, such as a qualified electronic signature. In such cases, it is often necessary to specify the media type.

To indicate that a binary sequence represents a Document Extract, use the following Media Type or MIME type: `application/mdoc+extract`.

---
eip: draft-eip-purpose-field-slots
title: Purpose Field for Canonical Storage Key
status: Draft
type: ERC
author: Richard Moore (@ricmoo)
created: 2021-06-25
requires: 191
---

## Simple Summary

This proposal describes a method to construct a distinguished
32-byte key which can be used for arbitrary application-specific
storage.


## Abstract

TBD.


## Motivation

The usecase that inspired this EIP was the desire to use EOA account
storage for storing per-account configuration.

As this is something that it is likely other EIPs may wish to do in the
future, there is a desire to not monopolize the account storage key-space
and make sure there is flexibility available in the future.


## Specification

To compute a key, each application should defined a payload which must be
32-bytes long, which can entangle additional properties required by that
application. This payload may be a bytes32 of 0 if unneeded.

The computation of the key is:

```
KECCAK( 0x1902 || pad(XXX, 3) || pad(applicationID, 32) || payload )
```


## Rationale

The use of hashing together multiple internal keys to create a final
storage key is commonly used in Solidity and other languages for their
mapping types. These always use a word-aligned (32 byte) word length
for each internal key.

The prefix `0x19` ensures that once hashed, it cannot be a valid transaction
hash and further more that it is an EIP-191 payload, with an unused
vesion byte of `0x02` (to be determined), which is currently unused
so no existing messages with this value will exist. 

The 3 byte padded prefix, ensures the length of the final preimage is
69 bytes, so will never collide with any existing key in contract storage.

The 3 byte prefix also ensures that any future specification that needs
to ammend or work in tandem with this EIP can simply choose a separate
3 byte prefix and be assured it will not collide with any existing key
created as a result of this EIP.

The ApplicationID is assumed to most commonly be an EIP, but future EIPs
could extend this by including a higher-order bit be set to allow a
custom ApplicationID.


## Backwards Compatibility

There are no known backwards compatibility issues as any payload of
69 bytes, is unlikely to be used for the purposes of addressing and
by prefixing with an unused EIP-191 version byte, no existing signed
messages would have used a compatible hash.


## Test Vectors

TBD


## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).

## Preamble

```
SEP: <to be assigned>
Title: One-time authentication between two Stellar accounts
Author: @gzigzigzeo, @nebolsin
Status: Draft
Created: 2018-05-03
```

## Simple Summary

This Mobius Ecosystem Proposal describes methods of Ledger-enabled secure authentication of one blockchain account on behalf of another. In some sense, it's OAuth2 for blockchain.

## Abstract

TODO

## Motivation

Currently, there are a couple of methods providing standard authentication to users of blockchain applications (see slip-0013), BitID. Unfortunately, such methods require dedicated wallet application installed on user’s device and/or simply are not compatible with Ledger hardware.

Our goal is to provide simple blockchain-based, Legder-compatible method of OAuth-like authentication for blockchain.

## Specification

Given we have two parties: let's call them user and developer.

Both of them have the following:

* User keypair and developer public key on user side.
* Developer keypair on developer side.
* Authentication endpoint (URL, HTTPS-enabled).

The process looks as following:

* User requests challenge from authentication endpoint using GET method.
* Challenge is the transaction meets following criterias:
  * It represents noop (manage meta data of some account or payment of minimal amount to user, for example).
  * It contains random part (random payment source account, for example).
  * It is signed by developers private key.
  * It contains issue time and expire time (time must be taken from developers server).
  * It never goes to blockchain and *must* be invalid in case somebody will try to send it to network.
* Challenge transaction is sent to user.
* User checks the signature of challenge transaction using known developers public key.
* If developers signature is correct, user signs transaction by his own private key (using Ledger, if he wants to).
* User sends it to developer endpoint using POST method along with his own public key.
* Developer checks:
  * Its own signature on received transaction.
  * User’s signature against received public key.
  * Transaction creation time bounds (current time must be within it).
  * The fact that transaction is not older than n (5-10 is recommened).
* If all checks are positive, transaction hash is:
  * Saved on the developers side along with session time bounds and user’s public key.
  * Sent to user and is considered as session token for future requests.

## Rationale
The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.

## Backwards Compatibility
All SEPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The SEP must explain how the author proposes to deal with these incompatibilities. SEP submissions without a sufficient backwards compatibility treatise may be rejected outright.

## Test Cases
Test cases for an implementation are mandatory for SEPs that are affecting consensus changes. Other SEPs can choose to include links to test cases if applicable.

## Implementation
The implementations must be completed before any SEPs is given status "Final", but it need not be completed before the SEP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.

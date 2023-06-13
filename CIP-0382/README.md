---
CIP: 382
Title: Integration of `keccak256` into Plutus 
Authors: Sergei Patrikeev <so.patrikeev@gmail.com>
Discussions-To: https://github.com/cardano-foundation/CIPs/pull/524
Comments-URI: https://github.com/cardano-foundation/CIPs/pull/524
Status: Draft
Category: Plutus
Type: Standards Track
Created: 2023-06-13
Post-History: None 
License: Apache-2.0
---

## Abstract
This CIP proposes an extension of the current Plutus functions to provide support for the [`keccak256`](https://keccak.team/files/Keccak-submission-3.pdf) hashing algorithm,
primarily to ensure compatibility with Ethereum's cryptographic infrastructure.

## Motivation

The [integration](https://github.com/input-output-hk/cardano-base/pull/252) of the ECDSA and Schnorr signatures over the secp256k1 curve into Plutus was a significant 
step towards interoperability with Ethereum and Bitcoin ecosystems. However, full compatibility is still impossible 
due to the absence of the `keccak256` hashing algorithm in Plutus interpreter, 
which is a fundamental component of Ethereum's cryptographic framework:
- data signing standard [EIP-712](https://eips.ethereum.org/EIPS/eip-712), 
- `keccak256` is the hashing algorithm underlying Ethereum's ECDSA signatures. 
- EVM heavily [depends](https://ethereum.github.io/yellowpaper/paper.pdf) on `keccak256` for internal state management

Adding `keccak256` to Plutus would enhance the potential for cross-chain solutions between Cardano and EVM-based blockchains.

## Specification
`keccak256` function is already available in [cardano-base](https://github.com/input-output-hk/cardano-base/blob/master/cardano-crypto-class/src/Cardano/Crypto/Hash/Keccak256.hs).
The changes would require a single function to be added to Plutus.

## Rationale
While the `keccak256` function might be implemented in on-chain scripts, doing so would be computationally unfeasible. 

## Path to Proposed
To move this Draft to Proposed, we need to cost the `keccak256` function.

### Trustworthiness of Implementations
The implementation of `keccak256` is based on the version found in the [cardano-base](https://github.com/input-output-hk/cardano-base/blob/master/cardano-crypto-class/src/Cardano/Crypto/Hash/Keccak256.hs) library.

### Costing of function
We performed some benchmarks (2,7 GHz Quad-Core Intel Core i7) on the `keccak256` that can be used to finalize the costing:
* Empty Input:
  * `keccak256("")` -> N ms  
* Short Fixed-Length Inputs (1, 2, 4, 8, 16, 32)
  * `keccak256("a")` -> N ms
  * `keccak256("aa")` -> N ms
  * `keccak256("aaaa")` -> N ms
  * `keccak256("aaaaaaaa")` -> N ms
  * `keccak256("aaaaaaaaaaaaaaaa")` -> N ms
  * `keccak256("aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa")` -> N ms
* Long Fixed-Length Inputs (256, 512, 1024) 
  * `keccak256("a" ^ 256)` -> N ms
  * `keccak256("a" ^ 512)` -> N ms
  * `keccak256("a" ^ 1024)` -> N ms
* Recursive hashing
  * `keccak256(keccak256("a"))` -> N ms
  * `keccak256(keccak256(keccak256("a")))` -> N ms

## Backward Compatibility
The addition of `keccak256` is backward compatible and will not interfere with existing functions within Plutus.

## Path to Active
Upon successful review and evaluation, the `keccak256` function is planned for release in an upcoming update.

## References
- keccak256: https://keccak.team/files/Keccak-submission-3.pdf
- Ethereum Yellow Paper: https://ethereum.github.io/yellowpaper/paper.pdf
- Ethereum standard on signing data: https://eips.ethereum.org/EIPS/eip-712
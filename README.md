# Substrate Carambola Node


## Build & Run

To build the chain, execute the following commands from the project root:

```
$ cargo build --release
```

To execute the chain, run:

```
$ ./target/debug/carambola-node --dev
```

The node also supports to use manual seal (to produce block manually through RPC).  
This is also used by the ts-tests:

```
$ ./target/debug/carambola-node --dev --manual-seal
```

## Run Example

**1. Create ERC20 Smart Contract to mint token**
* Compile smart contract in http://remix.ethereum.org/ 
* Bytecode:
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

// Learn more about the ERC20 implementation 
// on OpenZeppelin docs: https://docs.openzeppelin.com/contracts/4.x/erc20
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MyToken is ERC20 {
    constructor() ERC20("ETH Token", "SET") {
        _mint(msg.sender, 2**256 - 1);
    }
}
```
**2. Convert Substrate's account(H256 - 32 bytes) into Etherium Account (H160 - 20 bytes) in Development **
* Creating Account Alice follow subkey https://docs.substrate.io/v3/tools/subkey/
```
subkey inspect "//Alice"
```
** Result
```
Secret Key URI `//Alice` is account:
  Secret seed:       0xe5be9a5092b81bca64be81d212e7f2f9eba183bb7a90954f7b76361f6edb5c0a
  Public key (hex):  0xd43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d
  Public key (SS58): 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
  Account ID:        0xd43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d
  SS58 Address:      5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
```
** Alice's Etherium Account: Use first 20 bytes of the hex encoded from public key Substrate address
```
0xd43593c715fdd31c61141abd04a99fd6822c8558
```

* Creating Account Bob
```
subkey inspect "//Bob"
```
** Result
```

```
** Bob's Etherium Account: Use first 20 bytes of the hex encoded from public key Substrate address
```
0xd43593c715fdd31c61141abd04a99fd6822c8558
```

**3. Storage Slot**

** Installation dependencies
```
cd utils
npm i
```
** Usage
```
node ./utils <command> <parameters>
```
** Storage Slot for Alice
```
node ./utils --erc20-slot 0 0xd43593c715fdd31c61141abd04a99fd6822c8558
```

** Storage Slot for Bob
```
node ./utils --erc20-slot 0 
```

**4. Contract Creation**

Use the Polkadot UI **Extrinsics** app to deploy the contract from Alice's account (submit the extrinsic as a signed transaction) using **evm > create**

```
source: 0xd43593c715fdd31c61141abd04a99fd6822c8558
init: <raw contract bytecode, a very long hex value>
value: 0
gas_limit: 4294967295
gas_price: 1
nonce: <empty> {None}
```

**5. Check Alice Balance**


**6. Transfer Balance from Alice to Bob**


**7. Check Bob Balance**




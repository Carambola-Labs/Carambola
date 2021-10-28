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
**2. Convert Substrate's account(H256 - 32 bytes) into Etherium Account (H160 - 20 bytes)**


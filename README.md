# Substrate Carambola Node


## Build & Run

To build the chain, execute the following commands from the project root:

```
$ cargo build --release
```

To execute the chain, run:

```
$ ./target/release/carambola-node --dev
```

The node also supports to use manual seal (to produce block manually through RPC).  
This is also used by the ts-tests:

```
$ ./target/debug/carambola-node --dev --manual-seal
```

## Run Example


**0. Run Carambola node and frontend**

* Chạy node
```
$ ./target/release/carambola-node --dev
```

* Phục vụ cho việc Demo
```
https://polkadot.js.org/apps/
```


**1. Create ERC20 Smart Contract to mint token**
* Compile smart contract ERC20 https://github.com/Carambola-Labs/Carambola/blob/master/contracts/MyToken.sol in http://remix.ethereum.org/ 
* Bytecode:https://github.com/Carambola-Labs/Carambola/blob/master/contracts/bytecode_MyToken.txt
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
Secret Key URI `//Bob` is account:
  Secret seed:       0x398f0c28f98885e046333d4a41c19cee4c37368a9832c6502f6cfd182e2aef89
  Public key (hex):  0x8eaf04151687736326c9fea17e25fc5287613693c912909cb226aa4794f26a48
  Account ID:        0x8eaf04151687736326c9fea17e25fc5287613693c912909cb226aa4794f26a48
  Public key (SS58): 5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty
  SS58 Address:      5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty

```
** Bob's Etherium Account: Use first 20 bytes of the hex encoded from public key Substrate address
```
0x8eaf04151687736326c9fea17e25fc5287613693
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
node ./utils --erc20-slot 0 0x8eaf04151687736326c9fea17e25fc5287613693
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
** Event Trigger
```
evm.Created
A contract has been created at given [address]. 
H160
0xb49a2a41bbd68b6f49f03810f299d752875bcf89 // this is smart contract id
```

**5. Check Alice Balance**

Use the **Chain State** app to query **evm > accountStorage** and view the value associated with Alice's account

** first parameter: smart contract id
```
0x8a50db1e0f9452cfd91be8dc004ceb11cb08832f
```

** Second parameter: storage slot

```
0x045c0350b9cf0df39c4b40400c965118df2dca5ce0fbcf0de4aafc099aea4a14
```
** Result: Alice Balance (2 ** 256 -1)

```
0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff
```
**6. Transfer Balance from Alice to Bob**

** Use the **Developer** -> **Extrinsics** tab to invoke the **transfer(address, uint256)** function on the ERC-20 contract with **evm > call**
** **input** parameter: ABI encoded for specific function.

*** Go to https://abi.hashex.org/

*** Import ABI code : https://github.com/Carambola-Labs/Carambola/blob/master/contracts/MyToken.sol that is compiled in Remix or in https://github.com/Carambola-Labs/Carambola/blob/master/contracts/abi_MyToken.json

*** Choose function type : **transfer**  with:

```
recipient: 0x8eaf04151687736326c9fea17e25fc5287613693
amount: 221
```
*** Result ABI encode with **transfer** function:

```
0xa9059cbb0000000000000000000000008eaf04151687736326c9fea17e25fc528761369300000000000000000000000000000000000000000000000000000000000000dd
```

** **evm > call**
```
target: 0x8a50db1e0f9452cfd91be8dc004ceb11cb08832f // smart contract id
source: 0xd43593c715fdd31c61141abd04a99fd6822c8558 // Alice EVM's account
input: 0xa9059cbb0000000000000000000000008eaf04151687736326c9fea17e25fc528761369300000000000000000000000000000000000000000000000000000000000000dd
value: 0
gas_limit: 4294967295
gas_price: 1
```

** Event Trigger

```
evm.Executed
A [contract] has been executed successfully with states applied. 
H160
0x8a50db1e0f9452cfd91be8dc004ceb11cb08832f
```


**7. Check Bob Balance**

Use the **Chain State** app to query **evm > accountStorage** and view the value associated with Bob's account

** first parameter: smart contract id
```
0x8a50db1e0f9452cfd91be8dc004ceb11cb08832f
```

** Second parameter: storage slot

```
0xe15f03c03b19c474c700f0ded08fa4d431a189d91588b86c3ef774970f504892
```
** Result: Bob Balance

```
0x00000000000000000000000000000000000000000000000000000000000000dd (221)
```

** Result: Alice Balance

```
0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff22 (2 ** 32 -1 - 221)
```




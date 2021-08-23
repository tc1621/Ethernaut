This contract takes contributions in the form of small donations. If you have the most contributions you become the owner of the contract and can then withdrawl everything in the contract. At the beginning the owner is set to 1000 ether. 

In order to contribute we can send ```await contract.contribute.sendTransaction({value: toWei(".0001")})```

To take over the contract we need to send a transaction to the contract with a value greater then 0 and have contributions greater then 0. This can be done by ```await contract.sendTransaction({value: toWei(".001")})```  

We can check owner by calling ```await contract.owner()``` and we can see that the owner's address is the same as ours. Therefore we can withdraw all the eth.

```solidity
pragma solidity ^ 0.6 .0;

import '@openzeppelin/contracts/math/SafeMath.sol';

contract Fallback
{

	using SafeMath
	for uint256;
	mapping(address => uint) public contributions;
	address payable public owner;

	constructor() public
	{
		owner = msg.sender;
		contributions[msg.sender] = 1000 * (1 ether);
	}

	modifier onlyOwner
	{
		require(
			msg.sender == owner,
			"caller is not the owner"
		);
		_;
	}

	function contribute() public payable
	{
		require(msg.value < 0.001 ether);
		contributions[msg.sender] += msg.value;
		if (contributions[msg.sender] > contributions[owner])
		{
			owner = msg.sender;
		}
	}

	function getContribution() public view returns(uint)
	{
		return contributions[msg.sender];
	}

	function withdraw() public onlyOwner
	{
		owner.transfer(address(this).balance);
	}

	fallback() external payable
	{
		require(msg.value > 0 && contributions[msg.sender] > 0);
		owner = msg.sender;
	}
}
```
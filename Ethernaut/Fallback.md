### Description:

Look carefully at the contract's code below.

You will beat this level if

1. you claim ownership of the contract
2. you reduce its balance to 0

  Things that might help

- How to send ether when interacting with an ABI
- How to send ether outside of the ABI
- Converting to and from wei/ether units (see `help()` command)
- Fallback methods

---
### Contract:
```js
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Fallback {

  mapping(address => uint) public contributions;
  address public owner;

  constructor() {
    owner = msg.sender;
    contributions[msg.sender] = 1000 * (1 ether);
  }

  modifier onlyOwner {
        require(
            msg.sender == owner,
            "caller is not the owner"
        );
        _;
    }

  function contribute() public payable {
    require(msg.value < 0.001 ether);
    contributions[msg.sender] += msg.value;
    if(contributions[msg.sender] > contributions[owner]) {
      owner = msg.sender;
    }
  }

  function getContribution() public view returns (uint) {
    return contributions[msg.sender];
  }

  function withdraw() public onlyOwner {
    payable(owner).transfer(address(this).balance);
  }

  receive() external payable {
    require(msg.value > 0 && contributions[msg.sender] > 0);
    owner = msg.sender;
  }
}
```

---
### Vulnerability

In the code below, constructor determines that contributions of owner equal to 10^21 wei 
```js
constructor() {
    owner = msg.sender;
    contributions[msg.sender] = 1000 * (1 ether);
}
```

Function `contribute()` determines that if player send more ETH than owner, the ownership will be transferred to him.
```js
function contribute() public payable {
    require(msg.value < 0.001 ether);
    contributions[msg.sender] += msg.value;
    if(contributions[msg.sender] > contributions[owner]) {
      owner = msg.sender;
    }
}
```

Also,  `receive()` declared without `function` and it's payable. So, it is `fallback` function.
```js
receive() external payable {
    require(msg.value > 0 && contributions[msg.sender] > 0);
    owner = msg.sender;
}
```

---
### Exploitation 

1. Check owner address:
   
   ![[Pasted image 20231119225019.png]]
   
2. According to `contribute()` function, it requires to send value less then 0.001
   ![[Pasted image 20231119230641.png]]

3. Send to contract ether more than 0
   ![[Pasted image 20231119231421.png]]
   
4.  Check ownership address. Must be the same as a player's address.
   
   ![[Pasted image 20231119231630.png]]

5.  Finally reduce balance to 0 by withdrawing contract
   ![[Pasted image 20231119231939.png]]


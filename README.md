# PWNME-CTF-Mafia2-WriteUp

## Overview
During the first PWNME qualifications in this weekend (March 28 to 3), there were some pretty cool challenges. So I'm doing a write-up of some of them to deepen my skills and share my results with you.
In this second challenge, following Mafia1, we receive two Solidity files `Casino.sol` and `Setup.sol`.

## Casino.sol:

    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.26;

    contract CasinoPWNME {
    
      bool public isWinner;
    
    	uint256 public multiplier = 14130161972673258133;
    	uint256 public increment = 11367173177704995300;
    	uint256 public modulus = 4701930664760306055;
      uint private state;
    
      constructor (){
        state = block.prevrandao % modulus;
      }
    
      function checkWin() public view returns (bool) {
        return isWinner;
      }
    
      function playCasino(uint number) public payable  {
    
        require(msg.value >= 0.1 ether, "My brother in christ, it's pay to lose not free to play !");
        PRNG();
        if (number == state){
          isWinner = true;
        } else {
          isWinner = false;
        }
      }
      
      function PRNG() private{
        state = (multiplier * state + increment) % modulus;
      }
    
    }
  
## Setup.sol:

    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.26;
    
    import {CasinoPWNME} from "./Casino.sol";
    
    contract Setup {
    
        CasinoPWNME public casino;
    
        constructor() {
            casino = new CasinoPWNME();
        }
    
        function isSolved() public view returns (bool) {
            return casino.checkWin();
        }
        
    }


![mafia2_contracts](https://github.com/user-attachments/assets/43293360-aa2d-43d3-b843-f10956e62b85)

First we need to start the instance to retrieve the necessary information such as: `UUID`, `CASINO`, `RPC`, `PRIVATE_KEY`, `PLAYER`, `SETUP`, `TARGET`

![1](https://github.com/user-attachments/assets/b1db8e1d-3f72-497d-87b8-244a0bd86ba1)

To start the instance, let's use the generated input '14112411'.

![token1](https://github.com/user-attachments/assets/62c05e14-d7e7-4edb-93f2-b7ac1055a683)

Next, we need to check that foundry has been correctly installed using forge --version. Once it has been correctly installed, we can begin

![mafia2_forge_version](https://github.com/user-attachments/assets/f0bd66c3-c0dc-4b81-8465-f6c40e1fb39e)





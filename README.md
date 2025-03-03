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

To start the instance, let's use the generated input `14112411`

![token1](https://github.com/user-attachments/assets/62c05e14-d7e7-4edb-93f2-b7ac1055a683)

First we need to start the instance to retrieve the necessary information such as: `UUID`, `CASINO`, `RPC`, `PRIVATE_KEY`, `PLAYER`, `SETUP`, `TARGET`

![1](https://github.com/user-attachments/assets/b1db8e1d-3f72-497d-87b8-244a0bd86ba1)

We need to check that foundry has been correctly installed using `forge --version`. Once it has been correctly installed, we can begin

![mafia2_forge_version](https://github.com/user-attachments/assets/f0bd66c3-c0dc-4b81-8465-f6c40e1fb39e)

Next, our `Casino.sol` contract is presented with the following content. By observing it, we realize that the calculation of state can be `predictable` due to `PRNG`

PRNG updates the `state` function using the following operation:

      function PRNG() private{
        state = (multiplier * state + increment) % modulus;
      }

We can therefore `calculate` the `next state value` using the values already hard-coded in the contract

    bool public isWinner;

	uint256 public multiplier = 14130161972673258133;
	uint256 public increment = 11367173177704995300;
	uint256 public modulus = 4701930664760306055;
    uint private state;

First, we'll define the environment variables to simplify our calls.

![env_var](https://github.com/user-attachments/assets/9f3db42a-a195-46e7-9fb0-e757ec77f8dc)

To do this, we can use the python console to obtain the correct `uint`

// Retrieve from foundry: `cast storage $TARGET 4 --rpc-url $RPC`

![state](https://github.com/user-attachments/assets/ca706271-7c67-44b6-aa43-ff34c2caf3b3)

![uint_state](https://github.com/user-attachments/assets/f036e829-1616-4a5b-a655-d7777aba4db5)

With `0x in front`
Once the state has been retrieved from `cast`, we can use the `python console` to retrieve the `integer`

// Retrieve manually:

![console_entier](https://github.com/user-attachments/assets/6329a03e-f861-451e-82d7-e0dd8a243c66)

We can then perform the operation with the following syntax:

![operation](https://github.com/user-attachments/assets/d8355b0d-0d0d-4529-a0f2-0e6f9c5098e7)

As we now know the value of state, we can apply the logical equation to retrieve the deterministic integer `3318773149693343723`

![calcul](https://github.com/user-attachments/assets/3e102027-cd02-4fd2-81dd-a27b50b91a79)

As we now know the integer, we can send a transaction with `foundry` using `cast send` and calling the `playCasino(uint)` function, supplying the retrieved integer `3318773149693343723` and taking care to send a transaction of at least `0.1 ether`

![cast_send](https://github.com/user-attachments/assets/414daefc-99da-4336-8f86-5d984f5f1eb9)

	  function playCasino(uint number) public payable  {
	
	    require(msg.value >= 0.1 ether, "My brother in christ, it's pay to lose not free to play !");
	    PRNG();
	    if (number == state){
	      isWinner = true;
	    } else {
	      isWinner = false;
	    }
	  }

Then we can check by calling the `checkWin()` function from `cast call` if we've become a `winner`

![cast_call](https://github.com/user-attachments/assets/1a2e4215-05b6-4e2a-8181-6099eea21985)

We can now check that we have correctly become a winner: `01`

Now that we've won, we can correctly access the `VIP page` on the `web application` and retrieve the thread, including the `flag`

![FLAG](https://github.com/user-attachments/assets/445ab66c-bfc1-42a0-8a85-781e079a42cb)

	Credits: John 🏴‍☠️

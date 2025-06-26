// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract auction {

  mapping (address => uint ) people;

  address public manager = msg.sender;

  address[] winnerAddr;

  uint startingbet = 1 ether;

  uint time = block.timestamp + 2 minutes;

  uint public highestbet;

  event winnerbet(address Address);

  function Participate_Bet() public payable  {        

    require(manager != msg.sender,"manager can not be participate");
    require(time > block.timestamp,"time over");
    require(msg.value >= startingbet,"betting start from 1 ether");
    require(msg.value > highestbet,"sorry but this bet already taken");

    people[ msg.sender] =  msg.value;
    highestbet = people[ msg.sender];
    winnerAddr.push(msg.sender);

  } 

 function check_winner() public payable {

    require( block.timestamp > time,"wait for betting end");

    uint balance = address(this).balance;
    for (uint I = 0; I < winnerAddr.length-1;I++) 
    {
    balance -= people[winnerAddr[I]];
    payable (winnerAddr[I]).transfer( people[winnerAddr[I]]);
    people[winnerAddr[I]] = 0;
    }
      address temp;
      uint i = winnerAddr.length-1;
      temp = winnerAddr[i];
      emit winnerbet(temp);
      payable (manager).transfer(people[temp]);
      manager = temp;
  }

  function Restart_Bet(uint _Bet) public  {

   require(manager == msg.sender,"only owner can be restart");
   time = block.timestamp + 1 minutes;
   highestbet = _Bet;
   delete winnerAddr;

  }

  receive() external payable { 
    Participate_Bet();
  }


}

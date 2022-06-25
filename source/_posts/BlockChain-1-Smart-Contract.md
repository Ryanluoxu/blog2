---
title: BlockChain -1- Smart Contract
date: 2018-01-11 15:15:13
categories: BlockChain
tags: [BlockChain, Smart Contract, ETH]
---

# Basic
## What
- contract itself has an address and balance
- contains some functions for its logic
- the output will be post to blockchain 
- made by `Solidity`

<!-- more -->

## Editor
[Remix - Solidity IDE](https://remix.ethereum.org/)

# Sample
## RyanCoin.sol
```
pragma solidity ^0.4.0;
contract RyanCoin {
    address public creator; 
    mapping (address => uint) public balances;
    
    /**
     * - msg is global attribute.
     * - This constructor is 'create' button
     */
    function RyanCoin(){
        creator = msg.sender;
    }
    
    /**
     * - creator use this function to give other people coins
     * - make sure it is the creator running this function
     */
     function setCoin(address receiver, uint amount){
         if(msg.sender != creator) throw;
         balances[receiver] += amount;
     }
     
     /**
      * - other participants use this function to transfer coins
      */
      function transfer(address receiver, uint amount){
          if(amount > balances[msg.sender]) throw;
          balances[msg.sender] -= amount;
          balances[receiver] += amount;
      }
}
```

## Run
1. `Run`
2. Choose one of account as creator
3. `Create`

![smart_contract_demo1](/images/smart_contract_demo1.png)

Now RyanCoin is created with its own address.

## Test

### Give 1st account 10, 2nd account(self) 5
1. make sure select creator as account
2. input `"0xca35b7d915458ef540ade6068dfe2f44e8fa733c",10`
3. `setCoin`

![smart_contract_demo2](/images/smart_contract_demo2.png)

### Check balance
1. give `"0x14723a09acff6d2a60dcdf7aa4aff308fddc160c"`
2. `balances`

```
"0": "uint256: 5"
```

### Tranfer 2 coins to 2nd from 1st
1. select 1st account
2. give `"2nd address",2`
3. `transfer`

```
"address receiver": "0x14723a09acff6d2a60dcdf7aa4aff308fddc160c",
"uint256 amount": "2"
```

# Buy RyanCoin using ETH
## Code
```
pragma solidity ^0.4.0;
contract RyanCoin {
    mapping (address => uint) public balances;
    uint constant public PRICE = 2000000000000000000; // 2 ETH as PRICE
    
    function RyanCoin(){
    }
    
    function buyCoin() payable{     // pay ETH to create coin
        require(msg.value > 0 && msg.value % PRICE == 0);
        balances[msg.sender] += (msg.value / PRICE);
    }        
}
```
1. Set price.
2. Anyone can buy coin by paying ETH
3. `require` instead of `if`
4. `uint` require integer number of coin

## Run
1. Use 1st user to create contract
2. Choose 2nd user
3. Enter `10 ether` as value
4. `buyCoin`
5. 2nd user receive 5 RyanCoin



# Escrow.sol

```
pragma solidity ^0.4.0;

// temperature account where store money during transaction

contract Escrow {
    
    address public buyer;
    address public seller;
    address public arbiter;    // settle any dispute
    
    function Escrow(address _buyer, address _seller, address _arbiter){
        buyer = _buyer;
        seller = _seller;
        arbiter = _arbiter;
    }
    
    // we have different stages in this contract
    
    function confirmPayment() payable {
        require (msg.sender == buyer);
    }
}
```


# Auction.sol
```
pragma solidity ^0.4.0;

contract Auction {
    
    string public description;
    address public owner;
    address public maxBidder;
    uint public maxBid;
    uint public autionEnd;
    mapping(address => uint) pendingWithdrawals;
    
    event BidAccepted(address bidder, uint bidAmount);
    event AuctionCompleted(address winner, uint bidAmount);
    
    function Auction(uint durationMinutes, string _description){
        description = _description;
        autionEnd = now + (durationMinutes * 1 minutes); // `now` is global var
        owner = msg.sender;
    }
    
    // rollback function
    function() payable {
        // fire some log, invoke other function
        throw;
    }
    
    function bid() payable {
        require(msg.value > maxBid);
        require(now < autionEnd);
        
        if(maxBidder != 0){
            pendingWithdrawals[maxBidder] += maxBid;
        }
        
        maxBid = msg.value;
        maxBidder =  msg.sender;
        BidAccepted(maxBidder, maxBid);
    }
    
    function end(){
        require(msg.sender == owner);
        require(now >= autionEnd);
        
        pendingWithdrawals[owner] = maxBid;
        AuctionCompleted(maxBidder, maxBid);
    }
    
    function withdraw() returns (bool){
        uint amount = pendingWithdrawals[msg.sender];
        if(amount > 0){
            pendingWithdrawals[msg.sender] = 0;
            if(!msg.sender.send(amount)){
                pendingWithdrawals[msg.sender] = amount;
                return false;
            }
        }
        return true;
    }
}
```
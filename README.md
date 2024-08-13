# DengenToken Smart Contract
## Overview
The DengenToken smart contract is an ERC20 token implementation with additional functionalities for minting, burning, and redeeming tokens. The contract allows the owner to mint tokens, and any user can burn or redeem tokens. Redeemed tokens are recorded for each user and can be retrieved or printed.

### Features
- Minting: Only the contract owner can mint new tokens.
- Burning: Any token holder can burn their tokens.
- Redeeming: Token holders can redeem tokens, and the details of the redemption are stored.
- Transfer: Tokens can be transferred between addresses.
- Balance Check: The balance of any address can be checked.
- Redeemed Items Retrieval: Retrieve and print the details of redeemed tokens for any address.
  
### Prerequisites
- Node.js
- npm
- Truffle
- Ganache or any Ethereum test network
- OpenZeppelin Contracts

## Getting Started
### Installing
This program runs on EVM along with ".sol" as extension. We can either run it on websites like Remix or even on Visual Studios.
### Executing program
We need a solidity compatible virtual machine in order to run this program. Create a new file with ".sol" extension

    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.0;

    import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
    import "@openzeppelin/contracts/access/Ownable.sol";

    contract DengenToken is ERC20, Ownable {
         struct RedeemedItem {
        uint256 amount;
        string itemName;
        uint256 tokensRedeemed;
           }

  
    event TokensRedeemed(address indexed redeemer, string itemName, uint256 amount, uint256 tokensRedeemed);

   

    mapping(address => RedeemedItem[]) private redeemedItems;
      mapping(string => uint256) private itemCosts;
    // mapping(address => RedeemedItem[]) private redeemedItems;

    constructor() ERC20("Degen", "DGN") Ownable(msg.sender) {
        // Initialize item costs
        itemCosts["Chocolate"] = 1;
        itemCosts["Candies"] = 2;
        itemCosts["Ice cream"] = 3;
        itemCosts["Donuts"] = 6;
    }

   
    function mint(address to, uint256 amount) public onlyOwner {
        require(to != address(0), "Cannot mint to zero address");
        require(amount > 0, "Mint amount must be greater than zero");
        _mint(to, amount);
    }

    function burn(uint256 amount) public {
        require(amount > 0, "Burn amount must be greater than zero");
        _burn(_msgSender(), amount);
    }

 
      function redeem(string memory itemName, uint256 amount) public {
        require(amount > 0, "Redeem amount must be greater than zero");
        uint256 cost = itemCosts[itemName];
        require(cost > 0, "Item not recognized");
        uint256 totalCost = cost * amount;
        require(balanceOf(_msgSender()) >= totalCost, "Insufficient token balance");

        _burn(_msgSender(), totalCost);

        redeemedItems[_msgSender()].push(RedeemedItem({
            itemName: itemName,
            amount: amount,
            tokensRedeemed: totalCost
        }));

        emit TokensRedeemed(_msgSender(), itemName, amount, totalCost);
    }

     function getRedeemedItems(address account) public view returns (RedeemedItem[] memory) {
        require(account != address(0), "Query for zero address");
        return redeemedItems[account];
    }


        function printRedeemedTokens(address account) public view returns (string memory) {
        require(account != address(0), "Query for zero address");
        RedeemedItem[] memory items = redeemedItems[account];
        require(items.length > 0, "No redeemed tokens found");

        string memory result = "";
        for (uint256 i = 0; i < items.length; i++) {
            result = string(abi.encodePacked(
                result,
                "Redemption ", uintToString(i + 1), ": ",
                "Item: ", items[i].itemName, 
                " Amount: ", uintToString(items[i].amount),
                " Tokens Redeemed: ", uintToString(items[i].tokensRedeemed),
                "\n"
            ));
        }
        return result;
    }

     function uintToString(uint256 v) internal pure returns (string memory) {
        if (v == 0) {
            return "0";
        }
        uint256 digits;
        uint256 temp = v;
        while (temp != 0) {
            digits++;
            temp /= 10;
        }
        bytes memory buffer = new bytes(digits);
        while (v != 0) {
            digits -= 1;
            buffer[digits] = bytes1(uint8(48 + uint256(v % 10)));
            v /= 10;
        }
        return string(buffer);
    }

    function checkBalance(address account) public view returns (uint256) {
        require(account != address(0), "Query for zero address");
        return balanceOf(account);
    }

    // Function to transfer tokens
    function transferTokens(address from, address to, uint256 amount) public {
        require(amount > 0, "Transfer amount must be greater than zero");
        require(from != address(0), "Cannot transfer from zero address");
        require(to != address(0), "Cannot transfer to zero address");

        if (from == _msgSender()) {
            _transfer(from, to, amount);
        } else {
            uint256 currentAllowance = allowance(from, _msgSender());
            require(currentAllowance >= amount, "Transfer amount exceeds allowance");
            _approve(from, _msgSender(), currentAllowance - amount);
            _transfer(from, to, amount);
        }
    }
    }


## Usage

### Minting Tokens
Only the contract owner can mint new tokens. To mint tokens, call the mint function:
```
function mint(address to, uint256 amount) public onlyOwner
```
Example:
```
truffle console
DengenToken.deployed().then(instance => instance.mint("0xYourAddress", 1000))
```

### Burning Tokens
Any token holder can burn their tokens by calling the burn function:
```
function burn(uint256 amount) public
```
Example:
```
truffle console
DengenToken.deployed().then(instance => instance.burn(500, {from: "0xYourAddress"}))
```

### Redeeming Tokens
Token holders can redeem their tokens by calling the redeem function:
```
function redeem(uint256 amount) public
```
Example:
```
truffle console
DengenToken.deployed().then(instance => instance.redeem(10, {from: "0xYourAddress"}))
```

### Checking Balance
To check the balance of any address, call the checkBalance function:
```
function checkBalance(address account) public view returns (uint256)
```
Example:
```
truffle console
DengenToken.deployed().then(instance => instance.checkBalance("0xYourAddress"))
```

### Transferring Tokens
Tokens can be transferred between addresses by calling the transferTokens function:
```
function transferTokens(address from, address to, uint256 amount) public
```
Example:
```
truffle console
DengenToken.deployed().then(instance => instance.transferTokens("0xFromAddress", "0xToAddress", 100))
```

### Retrieving Redeemed Items
To retrieve the redeemed items for any address, call the getRedeemedItems function:
```
function getRedeemedItems(address account) public view returns (RedeemedItem[] memory)
```
Example:
```
truffle console
DengenToken.deployed().then(instance => instance.getRedeemedItems("0xYourAddress"))
```

### Printing Redeemed Tokens
To print the details of redeemed tokens for any address, call the printRedeemedTokens function:
```
function printRedeemedTokens(address account) public view returns (string memory)
```
Example:
```
truffle console
DengenToken.deployed().then(instance => instance.printRedeemedTokens("0xYourAddress"))
```



## Authors

Shiwani Agarwal
shivuagrwal23@gmail.com


## License

This project is licensed under the Sparsh Shandil License - see the LICENSE.md file for details




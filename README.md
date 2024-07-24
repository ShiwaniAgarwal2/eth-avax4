# DEGEN Token Smart Contract

We did create the ERC 20 TOKEN and we did name it as "Dengen Game Token"

## Description

This smart contract is designed to illustrate various essential features of Solidity, including:

1. Minting new tokens: The platform is able to create new tokens and distribute them to players as rewards.
   Only the owner has rights to mint tokens.
2. Transferring tokens: Players are able to transfer their tokens to others.
3. Redeeming tokens: Players are able to redeem their tokens for items in the in-game store.
4. Checking token balance: Players are able to check their token balance at any time.
5. Burning tokens: Anyone is able to burn tokens, that they own, that are no longer needed.

The contract includes:

1. Functions to mint and burn tokens.
2. A function to safely perform all the operations.
3. Owner-restricted functions.
4. A function to add items in inventory.
5. Custom error handling.


## Getting Started

### Installing

This program runs on EVM along with ".sol" as extension. We can either run it on websites like Remix or even on Visual Studios.

### Executing program

We need a solidity compatible virtual machine in order to run this program.
Create a new file with ".sol" extension

```
/ SPDX-License-Identifier: MIT
pragma solidity ^0.8;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/security/Pausable.sol";

contract DEGENToken is ERC20, Ownable, Pausable {
    mapping(address => bool) private _redeemers;
    mapping(uint256 => Item) private _storeItems;
    uint256 private _totalItems;

    struct Item {
        string name;
        uint256 price;
    }

    event ItemAdded(uint256 itemId, string name, uint256 price);
    event Redeemed(address indexed user, uint256 itemId, string itemName, uint256 itemPrice);

    constructor() ERC20("Degen", "DGEN") Ownable(msg.sender) Pausable() {
        _mint(msg.sender, 0);
        transferOwnership(msg.sender); // Set the owner to the deployer
    }

    function addStoreItem(string memory name, uint256 price) public onlyOwner whenNotPaused {
        _totalItems++;
        _storeItems[_totalItems] = Item(name, price);
        emit ItemAdded(_totalItems, name, price);
    }

    function mintDGENToken(address to, uint256 amount) public onlyOwner whenNotPaused {
        _mint(to, amount);
    }

    function burnDGENToken(uint256 amount) public whenNotPaused {
        _burn(msg.sender, amount);
    }

    function toggleRedeemer(address user) public onlyOwner {
        _redeemers[user] = !_redeemers[user];
    }

    function isRedeemer(address user) public view returns (bool) {
        return _redeemers[user];
    }

    function redeemItem(uint256 itemId) public whenNotPaused {
        require(_redeemers[msg.sender], "Not allowed to redeem");
        require(itemId <= _totalItems && itemId > 0, "Invalid item ID");
        Item storage item = _storeItems[itemId];
        require(balanceOf(msg.sender) >= item.price, "Insufficient balance");

        _transfer(msg.sender, owner(), item.price);
        _burn(msg.sender, item.price);
        emit Redeemed(msg.sender, itemId, item.name, item.price);
    }

    function getItemInfo(uint256 itemId) public view returns (string memory, uint256) {
        require(itemId <= _totalItems && itemId > 0, "Invalid item ID");
        Item memory item = _storeItems[itemId];
        return (item.name, item.price);
    }

    function getAllItems() public view returns (string[] memory, uint256[] memory) {
        string[] memory names = new string[](_totalItems);
        uint256[] memory prices = new uint256[](_totalItems);

        for (uint256 i = 1; i <= _totalItems; i++) {
            Item memory item = _storeItems[i];
            names[i - 1] = item.name;
            prices[i - 1] = item.price;
        }

        return (names, prices);
    }

    function getTotalSupply() public view returns (uint256) {
        return totalSupply();
    }

    function pause() public onlyOwner {
        _pause();
    }

    function unpause() public onlyOwner {
        _unpause();
    }

    function transferDGEN(address to, uint256 amount) public whenNotPaused returns (bool) {
        _transfer(msg.sender, to, amount);
        return true;
    }
}

---


## Help

Common Issues:
 1. Contract Compilation Errors:
   A. Ensure your Solidity version is compatible (0.8.18 or later).
   B. Check for syntax errors or typos in the contract.

2. Function Call Errors:

   A. Ensure you are using the correct contract address and bridging of meta mask.
   B. Check for access restrictions if encountering permission errors (e.g., onlyowner functions).


## Authors

Shiwani Agarwal
shivuagrwal23@gmail.com


## License

This project is licensed under the Sparsh Shandil License - see the LICENSE.md file for details




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
// SPDX-License-Identifier: MIT
pragma solidity 0.8.18;

contract GameToken {
    string public tokenName;
    string public tokenSymbol;
    uint16 public tokenDecimals;
    uint256 public totalTokenSupply;

    mapping(address => uint256) private userBalances;
    mapping(address => mapping(address => uint256)) private userAllowances;

    address public contractOwner;

    event TokenTransfer(address indexed from, address indexed to, uint256 value);
    event TokenApproval(address indexed owner, address indexed spender, uint256 value);
    event TokenMint(address indexed to, uint256 value);
    event TokenBurn(address indexed from, uint256 value);
    event ItemRedemption(address indexed from, string itemName);

    struct CollectibleItem {
        string itemName;
        uint256 itemPrice;
    }

    mapping(uint256 => CollectibleItem) public collectibleItems;

    modifier onlyContractOwner() {
        require(msg.sender == contractOwner, "Only the contract owner can perform this action.");
        _;
    }

    constructor() {
        tokenName = "Game Token";
        tokenSymbol = "GTKN";
        tokenDecimals = 18;
        totalTokenSupply = 0;
        contractOwner = msg.sender;
        addItem(0, "Golden Sword", 200);
        addItem(1, "Shield", 100);
        addItem(2, "Magic Wand", 2000);
        addItem(3, "Dragon Egg", 1000);
    }

    function getUserBalance(address account) external view returns (uint256) {
        return userBalances[account];
    }

    function transferTokens(address recipient, uint256 amount) external returns (bool) {
        require(amount <= userBalances[msg.sender], "Insufficient balance.");

        userBalances[msg.sender] -= amount;
        userBalances[recipient] += amount;

        emit TokenTransfer(msg.sender, recipient, amount);
        return true;
    }

    function transferTokensFrom(address sender, address recipient, uint256 amount) external returns (bool) {
        require(amount <= userBalances[sender], "Insufficient balance.");
        require(amount <= userAllowances[sender][msg.sender], "Insufficient allowance.");

        userBalances[sender] -= amount;
        userBalances[recipient] += amount;
        userAllowances[sender][msg.sender] -= amount;

        emit TokenTransfer(sender, recipient, amount);
        return true;
    }

    function mintTokens(address to, uint256 amount) external onlyContractOwner {
        totalTokenSupply += amount;
        userBalances[to] += amount;

        emit TokenMint(to, amount);
        emit TokenTransfer(address(0), to, amount);
    }

    function burnTokens(uint256 amount) external {
        require(amount <= userBalances[msg.sender], "Insufficient balance.");

        userBalances[msg.sender] -= amount;
        totalTokenSupply -= amount;

        emit TokenBurn(msg.sender, amount);
        emit TokenTransfer(msg.sender, address(0), amount);
    }

    function redeemItem(uint256 itemId) external returns (string memory) {
        require(userBalances[msg.sender] > 0, "Insufficient balance.");
        require(collectibleItems[itemId].itemPrice > 0, "Invalid item ID.");

        uint256 redemptionAmount = collectibleItems[itemId].itemPrice;
        require(userBalances[msg.sender] >= redemptionAmount, "Insufficient balance to redeem the item.");

        userBalances[msg.sender] -= redemptionAmount;

        emit ItemRedemption(msg.sender, collectibleItems[itemId].itemName);

        return collectibleItems[itemId].itemName;
    }

    function addItem(uint256 itemId, string memory itemName, uint256 itemPrice) public onlyContractOwner {
        collectibleItems[itemId] = CollectibleItem(itemName, itemPrice);
    }
}
```


## Help

Common Issues:
 1. Contract Compilation Errors:
   A. Ensure your Solidity version is compatible (0.8.18 or later).
   B. Check for syntax errors or typos in the contract.

2. Function Call Errors:

   A. Ensure you are using the correct contract address and bridging of meta mask.
   B. Check for access restrictions if encountering permission errors (e.g., onlyowner functions).


## Authors


Contributors names and contact info


ex. Sparsh Shandil 
ex. [@Allegiantshark](https://linktr.ee/allegiantshark)


## License

This project is licensed under the Sparsh Shandil License - see the LICENSE.md file for details

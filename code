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

# Subscription-Service
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

import "@openzeppelin/contracts/access/Ownable.sol";

contract BaseSubscription is Ownable {
    uint256 public monthlyFee = 0.05 ether;
    mapping(address => uint256) public expiry;

    event Subscribed(address user, uint256 until);

    function subscribe() external payable {
        require(msg.value >= monthlyFee, "Insufficient payment");
        expiry[msg.sender] = block.timestamp + 30 days;
        emit Subscribed(msg.sender, expiry[msg.sender]);
    }

    function renew() external payable {
        require(msg.value >= monthlyFee, "Insufficient payment");
        if (expiry[msg.sender] > block.timestamp) {
            expiry[msg.sender] += 30 days;
        } else {
            expiry[msg.sender] = block.timestamp + 30 days;
        }
    }

    function isActive(address user) external view returns (bool) {
        return expiry[user] > block.timestamp;
    }
}

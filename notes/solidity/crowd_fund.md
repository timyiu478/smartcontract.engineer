---
title: "Delegate Call"
tags: ["Solidity", "App"]
---

# Description


Implement a crowd funding contract.

- User creates a campaign to raise funds, setting start time, end time and goal (amount to raise).
- Other users can pledge to the campaign. This will transfer tokens into the contract.
- If the goal is reached before the campaign ends, the campaign is successful. Campaign creator can claim the funds.
- Otherwise, the goal was not reached in time, users can withdraw their pledge.


# Tasks

## 1

Complete launch

```
function launch(uint256 goal, uint32 startAt, uint32 endAt) external {
    // code
}
```

- This function will create a new campaign.
- Check that startAt is greater than or equal to block.timestamp
- Check that startAt is less tnan or equal to endAt
- Check that endAt is less than or equal to 90 days from block.timestamp
- Increment count and use this as an id for the new campaign
- Store the new campaign in the mapping campaigns
- Set creator to msg.sender
- Set pledged to 0
- Set claimed to false
- Emit the event Launch

## 2

Complete cancel

- Campaign creator can delete an campaign if
- msg.sender is the campaign creator
- Campaign has not yet started
- Delete campaign having the id id
- Emit Cancel

## 3

Users can pledge to a campaign

- Check that campaign has started
- Check that campaign has not ended
- Transfer token from msg.sender to this contract for the amount amount
- Increment pledged for campaign having the id id
- Update pledgedAmount so that msg.sender can later withdraw if the campaign is not successful
- Emit the event Pledge

## 4

User can withdraw up to the amount that they pledged while the campaign is active.

- Check campaign has not ended
- Transfer amount amount of tokens back to the user
- Decrement the amount pledged for campaign with id id
- Decrement pledgedAmount
- Emit the event Unpledge

## 5

Campaign creator can claim funds if the campaign succesfully raise the funds.

- Only campaign creator can call this function
- Cannot call if campaign has not ended
- Total amount pledged must be greater than or equal to goal
- Cannot call claim more than once
- Transfer pledged amount to campaign creator
- Emit the event Claim

## 6

Users can withdraw all of their pledge amount from an unsuccessful campaign.

- Campaign must have ended
- Total amount pledged must be less than campaign goal.
- Refund token to msg.sender
- Emit the event Refund

# Solution

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

import {IERC20} from "sce/sol/IERC20.sol";

contract CrowdFund {
    event Launch(
        uint256 id,
        address indexed creator,
        uint256 goal,
        uint32 startAt,
        uint32 endAt
    );
    event Cancel(uint256 id);
    event Pledge(uint256 indexed id, address indexed caller, uint256 amount);
    event Unpledge(uint256 indexed id, address indexed caller, uint256 amount);
    event Claim(uint256 id);
    event Refund(uint256 id, address indexed caller, uint256 amount);

    struct Campaign {
        // Creator of campaign
        address creator;
        // Amount of tokens to raise
        uint256 goal;
        // Total amount pledged
        uint256 pledged;
        // Timestamp of start of campaign
        uint32 startAt;
        // Timestamp of end of campaign
        uint32 endAt;
        // True if goal was reached and creator has claimed the tokens.
        bool claimed;
    }

    IERC20 public immutable token;
    // Total count of campaigns created.
    // It is also used to generate id for new campaigns.
    uint256 public count;
    // Mapping from id to Campaign
    mapping(uint256 => Campaign) public campaigns;
    // Mapping from campaign id => pledger => amount pledged
    mapping(uint256 => mapping(address => uint256)) public pledgedAmount;

    constructor(address _token) {
        token = IERC20(_token);
    }

    function launch(uint256 goal, uint32 startAt, uint32 endAt) external {
        require(startAt >= block.timestamp, "startAt is too old");
        require(endAt >= startAt, "startAt should <= endAt");
        require(endAt <= block.timestamp + 90 days, "Campaign period should not exceed 90 days");

        count += 1;
        Campaign memory campaign = Campaign(msg.sender, goal, 0, startAt, endAt, false);
        campaigns[count] = campaign;
        
        emit Launch(count, msg.sender, goal, startAt, endAt);
    }

    function cancel(uint256 id) external {
        require(msg.sender == campaigns[id].creator);
        require(block.timestamp < campaigns[id].startAt, "Campaign is started");
        
        delete campaigns[id];
        
        emit Cancel(id);
    }

    function pledge(uint256 id, uint256 amount) external {
        require(block.timestamp >= campaigns[id].startAt, "Campaign is not started");
        require(block.timestamp <= campaigns[id].endAt, "Campaign is ended");
        
        bool success = token.transferFrom(msg.sender, address(this), amount);
        
        require(success == true, "Failed to transfer token");
        
        pledgedAmount[id][msg.sender] += amount;
        campaigns[id].pledged += amount;
        
        emit Pledge(id, msg.sender, amount);
    }

    function unpledge(uint256 id, uint256 amount) external {
        require(block.timestamp >= campaigns[id].startAt, "Campaign is not started");
        require(block.timestamp <= campaigns[id].endAt, "Campaign is ended");
        require(pledgedAmount[id][msg.sender] >= amount, "Insuffient pledged amount");
        
        bool success = token.transfer(msg.sender, amount);
        
        require(success == true, "Failed to transfer token");
        
        pledgedAmount[id][msg.sender] -= amount;
        campaigns[id].pledged -= amount;
        
        emit Unpledge(id, msg.sender, amount);
    }

    function claim(uint256 id) external {
        require(msg.sender == campaigns[id].creator, "Only creator can cliam");
        require(block.timestamp > campaigns[id].endAt, "Campaign is not ended");
        require(campaigns[id].pledged >= campaigns[id].goal, "The goal is not achieved");
        require(campaigns[id].claimed == false, "Campaign is claimed");
        
        campaigns[id].claimed = true;
        
        bool success = token.transfer(campaigns[id].creator, campaigns[id].pledged);
        
        require(success == true, "Failed to transfer token");

        
        emit Claim(id);
    }

    function refund(uint256 id) external {
        require(block.timestamp > campaigns[id].endAt, "Campaign is not ended");
        require(campaigns[id].pledged < campaigns[id].goal, "The goal is achieved");
        require(campaigns[id].claimed == false, "Campaign is claimed");
        
        uint256 amount = pledgedAmount[id][msg.sender];
        delete pledgedAmount[id][msg.sender];
        
        bool success = token.transfer(msg.sender, amount);
        
        require(success == true, "Failed to transfer token");
        
        emit Refund(id, msg.sender, amount);
    }
}
```

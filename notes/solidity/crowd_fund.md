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

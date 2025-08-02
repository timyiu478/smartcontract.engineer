---
title: "ChainLink Price Oracle"
tags: ["Solidity", "ChainLink", "DeFi"]
---

# Description

Real-world data, such as price of gold, foreign exchange rate and weather data are not accessible to smart contracts.

Unless you use an oracle. Oracles provide real-world data to smart contracts.

Use Chainlink oracle to get the price of ETH.

# Tasks

Get the latest price of ETH in USD. Write your code inside the function below.


- function getPrice() public view returns (uint256) {}
- Get the price of ETH by calling latestRoundData() with the interface AggregatorV3Interface at the contract ETH_USD. See below for the interface. The price is stored in the second output int256 answer.
- Require that the price was updated within the last 3 hours
- Require that answer is greater than equal to 0
- Return the price with 18 decimals. ETH / USD price oracle has 8 decimals.

```sol
interface AggregatorV3Interface {
    function latestRoundData()
        external
        view
        returns (
            // Round id the answer was created in
            uint80 roundId,
            // Answer - the price
            int256 answer,
            // Timestamp when the round started
            uint256 startedAt,
            // Timestamp when the round was updated
            uint256 updatedAt,
            // Legacy round id - no longer needed
            uint80 answeredInRound
        );
}
```

# Solution

todo

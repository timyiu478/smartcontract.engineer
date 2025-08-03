---
title: "ChainLink Price Oracle"
tags: ["Solidity", "ChainLink", "DeFi", "Real-World Assets"]
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

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

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

contract PriceOracle {
    // Chainlink price feed for ETH / USD
    address private constant ETH_USD =
        0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419;

    function getPrice() public view returns (uint256) {
        AggregatorV3Interface priceFeed = AggregatorV3Interface(ETH_USD);
        (uint80 roundId, int price, uint startedAt, uint updatedAt, uint80 answeredInRound) = priceFeed.latestRoundData();
        require((block.timestamp - updatedAt) <= 3 hours, "the price was not updated within the last 3 hours");
        require(price >= 0, "the price needs to great than or equal to 0");
        return uint256(price) * 1e10;
    }
}
```

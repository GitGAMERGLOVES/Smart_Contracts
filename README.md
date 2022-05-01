# Smart_Contracts
Solidity, Blockchain, and Smart Contract Course – Beginner to Expert Python Tutorial

// SPDX-License-Identifier: MIT

// Crowd Sourcing Application

pragma solidity >=0.6.6 <0.9.0;

import "@chainlink/contracts/src/v0.6/interfaces/AggregatorV3Interface.sol";
import "@chainlink/contracts/src/v0.6/vendor/SafeMathChainlink.sol";

contract FundMe {

    mapping(address => uint256) public addressToAmountFunded;
    address[] public funders;
    address public owner;


    constructor() public {
        owner = msg.sender;
    }

    function fund() public payable {
        // $50 
        uint256 minimumUSD = 50 * 10 * 18;
        // 1gwei < $50
        require(getConversionRate(msg.value) >= minimumUSD, "You need to spend more ETH!");
        addressToAmountFunded[msg.sender] += msg.value;
        funders.push(msg.sender); 
        // what the ETH -> USD conversion rate
    }

    function getVersion() public view returns (uint256) {
        AggregatorV3Interface priceFeed = AggregatorV3Interface(0x8A753747A1Fa494EC906cE90E9f37563A8AF630e);
        return priceFeed.version();
    }

    function getPrice() public view returns (uint256) {
        AggregatorV3Interface priceFeed = AggregatorV3Interface(0x8A753747A1Fa494EC906cE90E9f37563A8AF630e);
        (,int256 answer,,,) = priceFeed.latestRoundData();
        return uint256(answer * 10000000000);
        // 2740.32000000
    }
    // 10000000000
    function getConversionRate(uint256 ethAmount) public view returns (uint256) {
        uint256 ethPrice = getPrice();
        uint256 ethAmountInUsd = (ethPrice * ethAmount) / 1000000000000000000;
        return ethAmountInUsd;
        // 0.000027679289432000
    }

    modifier onlyOwner {
        require(msg.sender == owner);
        _;
    }

    function withdraw() payable public {
        // only want the contract admin/owner
        // require msg.sender = owner
        require(msg.sender == owner);
        msg.sender.transfer(address(this).balance);
        for (uint256 funderIndex=0; funderIndex < funders.length; funderIndex++) {
            address funder = funders[funderIndex];
            addressToAmountFunded[funder] = 0;
        }
        funders = new address[](0);
    }
}

// Addresses Hashed below:
// 0xE3E8ceD67Dd9AC1BeB7D6B18CD058F3c63217f23
// 0x93423b0dBF6c14135DA71Fe0E7DBd8DB0e993d3d
// 0xC9D27A79Bc6b6C2dc00a3cbfB3c04a7eBBCFB7c7
// 0x0AADCDc2b7E2a93ce6E0F17279ddB13d2340fc89
// 0xbc73827A347faAe4b73025A944D77703F1C540F2
// 0xfE59B892641C54d0aB5D998583f742B52B8C5ffa
// 0xa08A77821ccb968F66B476fF8362A82543D40F3C
// 0x5BB402A7fe3493E82954E054dA43E75c66b5e89c
// 0x94000c8bD3F282ac6dE62190fc6C4CfeE268880d
// 0xA301aD2fEBfe03B2fDba431DC9A3C47A4e5a6003
// 0xA6122d980F6BC20Ea10B9a63E36d5B0d9A8e19c1
// 0x4dd8c28c3CFd8631099210fa3a96e0fB06B8c2eA
// 0x6c273622cC84852aaC8E052Bd387d046833B9205

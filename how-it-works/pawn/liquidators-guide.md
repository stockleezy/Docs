# ㊙ Liquidator's Guide

{% hint style="info" %}
All the contents of the contract can be found here :&#x20;

[https://github.com/BlockNG-Foundation/LAW-metaverse-contract-core/tree/main/Pawn](https://github.com/BlockNG-Foundation/LAW-metaverse-contract-core/tree/main/Pawn)
{% endhint %}

#### Useful Contract Addresses

DegenBox : [0xDFD09C4A1Fd999F6e8518398216c53fcEa6f4020](https://www.smartscan.cash/address/0xDFD09C4A1Fd999F6e8518398216c53fcEa6f4020)

WBCHMarket : [0x3F562957b199d6362B378dBa5e3b45EE6fe77779](https://www.smartscan.cash/address/0x3F562957b199d6362B378dBa5e3b45EE6fe77779)

LAWMarket : [0xd46e5a9Cd7A55Bf8d3582Ff66218aD3e63462506](https://www.smartscan.cash/address/0xd46e5a9Cd7A55Bf8d3582Ff66218aD3e63462506)

MasterContract : 0xA01d276d9e8D356AdaBAda8b60916489BD4f51a8



#### Approve Markets

Sample Code：

```
//
DegenBox.setMasterContractApproval(address(this), market.masterContract(), true, 0, 0, 0);

```

#### Deposing lawUSD(Optional if not use swapper)

Depending on the amount of debt to be liquidated, it is necessary to fund lawUSD in DegenBox.

Sample Code：

```
//need to approve first
lawUSD.approve(address(box), num);
box.deposit(lawUSD, address(this), address(this), num, 0);
```



#### Liquidation

{% hint style="info" %}
Current liquidation is only available to a limited number of addresses.
{% endhint %}

```
//
interface Market {

    function onlyWhiteLiquidator() external view returns (bool);
    //current is true, will open when the price of LAW stabilizes
    
    function positionInfo(address user) external view
    returns 
    (uint256 deposited, 
        uint256 depositedShare, 
        uint256 depositedValue, 
        uint256 borrowed, 
        uint256 borrowedPart, 
        uint256 borrowedShare
    )

    function liquidate(
        address[] calldata users, // user need to be liquidated
        uint256[] calldata maxBorrowParts, // user borrow part
        address to, // move collateral to
        ISwapper swapper // could be zero if not use swapper
    ) external;
    

}
```

In general, you can use positionInfo to determine whether the user's position is ready for liquidation and to obtain the user's borrowpart.

If the collateral allows, the entire borrowpart of the user can be liquidated.

Once the user to be liquidated has been identified, the liquidate method can be called to perform the liquidation.

Note: If a swapper is not used, the collateral will go to the DegenBox of the "to" address.

#### Swapper

Swapper can convert collateral to lawUSD so that you can liquidate even if you don't have enough lawUSD.

An example of a Swapper：

[https://github.com/Abracadabra-money/magic-internet-money/blob/main/contracts/swappers/SushiSwapSwapper.sol](https://github.com/Abracadabra-money/magic-internet-money/blob/main/contracts/swappers/SushiSwapSwapper.sol)

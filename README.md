# 📄 SimpleSwap Smart Contract Documentation

This document provides a overview of the **SimpleSwap** smart contract written in Solidity. The contract implements a minimal decentralized exchange (DEX) for swapping two ERC-20 tokens and providing/removing liquidity.

---

## 🔧 Overview

The `SimpleSwap` contract mimics core functionalities of protocols like Uniswap V2. It allows users to:

- Add liquidity to a token pair
- Remove liquidity from a token pair
- Swap tokens
- Get price and output estimations

This contract also issues LP tokens (ERC-20) that represent a user's share of the liquidity pool.

---

## 🧩 State Variables

### Public `immutable` variables

| Name     | Type      | Description                    |
| -------- | --------- | ------------------------------ |
| `TokenA` | `address` | Address of token A in the pair |
| `TokenB` | `address` | Address of token B in the pair |

### Private variables

| Name               | Type                          | Description                                |
| ------------------ | ----------------------------- | ------------------------------------------ |
| `reserveA`         | `uint256`                     | Current reserve of token A in the contract |
| `reserveB`         | `uint256`                     | Current reserve of token B in the contract |
| `liquidityBalance` | `mapping(address => uint256)` | LP token balances of users                 |

---

## 🚀 Events

### `LiquidityAdded`

Emitted when a user adds liquidity.

```solidity
LiquidityAdded(address provider, address TokenA, address TokenB, uint256 amountA, uint256 amountB, uint256 liquidity)
```

### `LiquidityRemoved`

Emitted when a user removes liquidity.

```solidity
LiquidityRemoved(address provider, address TokenA, address TokenB, uint256 amountA, uint256 amountB, uint256 liquidity)
```

### `SwapExecuted`

Emitted after a successful token swap.

```solidity
SwapExecuted(address sender, uint256 amountIn, uint256 amountOut, address to)
```

---

## ⚙️ Core Functions

### `constructor(address _tokenA, address _tokenB)`

Initializes the pair with two token addresses. These cannot be changed.

### `addLiquidity(...)`

Allows users to add liquidity for the token pair. It validates ratios, transfers tokens to the contract, and mints LP tokens.

### `removeLiquidity(...)`

Allows LP token holders to burn their liquidity tokens and retrieve the underlying tokens.

### `swapExactTokensForTokens(...)`

Swaps an exact amount of one token for another, following the constant product formula and applying a fee.

### `getPrice(...)`

Returns the price of one token in terms of the other, scaled to 18 decimals.

### `getAmountOut(uint amountIn, uint reserveIn, uint reserveOut)`

Pure function that calculates the expected output amount given reserves and input, using Uniswap's formula with 0.3% fee.

### `_updateReserves()`

Private function that updates internal reserve variables to match the contract's actual token balances.

### `_sqrt(uint256 y)`

Internal function to compute the square root using the Babylonian method (used to compute initial LP tokens).

---


## 🧚 Token Implementations

### TokenA

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract TokenA is ERC20 {
    constructor() ERC20("Token A", "TKA") {
        _mint(msg.sender, 100000 * 1e18);
    }
}
```

### TokenB

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract TokenB is ERC20 {
    constructor() ERC20("Token B", "TKB") {
        _mint(msg.sender, 100000 * 1e18);
    }
}
```

These ERC-20 tokens are used for liquidity provisioning and swapping in the SimpleSwap contract.

---

## 💡 Example Usage Scenario

Assume the following example:

- `TokenA` address: `0xABC...123`
- `TokenB` address: `0xDEF...456`

### 1. Add Liquidity

```solidity
SimpleSwap.addLiquidity(
    0xABC...123,  // tokenA
    0xDEF...456,  // tokenB
    5000 * 1e18,  // amountADesired
    10000 * 1e18, // amountBDesired
    4900 * 1e18,  // amountAMin
    9800 * 1e18,  // amountBMin
    msg.sender,   // to
    block.timestamp + 300 // deadline
);
```

### 2. Swap Tokens

```solidity
SimpleSwap.swapExactTokensForTokens(
    1000 * 1e18,                // amountIn
    1900 * 1e18,                // amountOutMin
    [tokenA, tokenB],          // path
    msg.sender,                // to
    block.timestamp + 300      // deadline
);
```

### 3. Remove Liquidity

```solidity
SimpleSwap.removeLiquidity(
    tokenA,
    tokenB,
    100 * 1e18,      // LP tokens
    90 * 1e18,       // amountAMin
    180 * 1e18,      // amountBMin
    msg.sender,      // to
    block.timestamp + 300 // deadline
);
```

---

## 🧪 Notes

- Token approvals are required before calling `addLiquidity` or `swap`.
- Always monitor `reserveA` and `reserveB` before performing swaps.

---


## 👨‍💻 Author

Juan Carlos Mori Diaz.


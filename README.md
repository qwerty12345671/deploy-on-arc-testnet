# deploy-on-arc-testnet
Step-by-step guide to deploying smart contracts on Arc Testnet with Foundry and Vscode
# Deploy a Solidity Contract on Arc Testnet

This guide walks you through deploying a smart contract on the **Arc Testnet** using **Foundry**.  
By the end, you’ll understand how to configure RPC, deploy using VS Code, and interact with your contract.

---

## Overview

Arc is a **stablecoin-native blockchain** that uses **USDC as gas**.  
It is currently in **testnet phase**, and this tutorial will show you how to:

- Set up your wallet and RPC
- Configure Foundry for Arc
- Write and deploy a Solidity contract
- Interact with it directly from VS Code

---

## Prerequisites

Before you begin, ensure you have:

- A GitHub account
- Node.js and Git installed
- VS Code installed
- A wallet like MetaMask or OKX Wallet

---

## 1. Add Arc Testnet RPC to Your Wallet

### MetaMask / OKX Wallet

1. Open your wallet → go to **Networks → Add Network Manually**
2. Fill in the following details:

| Field | Value |
|-------|--------|
| Network Name | Arc Testnet |
| RPC URL | https://rpc.testnet.arc.network |
| Chain ID | 5042002  |
| Currency Symbol | USDC |
| Block Explorer | https://testnet.arcscan.app |

3. Save it.  
You can now switch to **Arc Testnet** in your wallet.

---
## 2. Set Up Your Development Environment

### Install Foundry

```bash
curl -L https://foundry.paradigm.xyz | bash
foundryup
```

---

## 3. Create a New Project

Initialize a new Foundry project and move into the directory:

```bash
forge init hello-arc && cd hello-arc
```

## 4. Add Environment Variables

Create a `.env` file in your project root:

```bash
ARC_TESTNET_RPC_URL="https://rpc.testnet.arc.network"
PRIVATE_KEY="0xYourPrivateKeyHere"
```

> ⚠️ **Never commit your `.env` file to GitHub.** Keep it private.

---

## 5. Write a Smart Contract

Delete the default `Counter.sol` and create a new file:

`src/HelloArchitect.sol`

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.30;

contract HelloArchitect {
    string private greeting;
    event GreetingChanged(string newGreeting);

    constructor() {
        greeting = "Hello Architect!";
    }

    function setGreeting(string memory newGreeting) public {
        greeting = newGreeting;
        emit GreetingChanged(newGreeting);
    }

    function getGreeting() public view returns (string memory) {
        return greeting;
    }
}
```

---

## 6. Compile the Contract

```bash
forge build
```

---

## 7. Deploy on Arc Testnet

### Step 1 — Fund Your Wallet

Visit [https://faucet.circle.com](https://faucet.circle.com),  
select **Arc Testnet**, paste your wallet address, and request testnet USDC.

---

### Step 2 — Deploy via VS Code Terminal

1. Open the project in **VS Code**  
2. Open a new terminal and run:

```bash
source .env
forge create src/HelloArchitect.sol:HelloArchitect \
  --rpc-url $ARC_TESTNET_RPC_URL \
  --private-key $PRIVATE_KEY \
  --broadcast
```

---

## 8. Verify Deployment on Arc Explorer

Go to [https://testnet.arcscan.app](https://testnet.arcscan.app)  
Paste the transaction hash from your deployment output to view the transaction.

---

## 9. Interact with the Contract

Call the `getGreeting()` function directly using Foundry’s `cast` command:

```bash
cast call <YOUR_CONTRACT_ADDRESS> "getGreeting()(string)" \
  --rpc-url $ARC_TESTNET_RPC_URL
```

**Output:**
```
Hello Architect!
```

---

## 10. Deploy Using VS Code Only (Alternative)

If you prefer deploying without the command line:

1. **Install Hardhat**

```bash
npm install --save-dev hardhat
npx hardhat
```

2. **Configure your Hardhat network** in `hardhat.config.ts`:

```ts
networks: {
  arc: {
    url: "https://rpc.testnet.arc.network",
    accounts: [process.env.PRIVATE_KEY!],
  },
},
```

3. **Create a deploy script** under `scripts/deploy.ts`:

```ts
import { ethers } from "hardhat";

async function main() {
  const Contract = await ethers.deployContract("HelloArchitect");
  await Contract.waitForDeployment();

  console.log("Deployed to:", await Contract.getAddress());
  console.log("Transaction hash:", Contract.deploymentTransaction()?.hash);
}

main().catch((error) => {
  console.error(error);
  process.exit(1);
});
```

4. **Run the deploy:**

```bash
npx hardhat run scripts/deploy.ts --network arc
```

---

## 11. Next Steps

- Extend your contract with on-chain logic  
- Explore Arc’s USDC-based gas model  
- Deploy a frontend using React + Wagmi + RainbowKit  
- Experiment with other Move- or EVM-compatible tools

---

## License

MIT © 2025  
Created by [qwerty12345671]

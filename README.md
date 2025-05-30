---
title: "Precompile Example Usage"
description: "Learn how to interact with Sei's precompiles through ethers.js, including setup, claiming assets with solo precompile."
keywords: ["precompile usage", "ethers.js", "solo precompile", "claim", "sei contract interfaces"]
---


# Example Usage

The Sei precompiles can be used like any standard smart contract on the EVM. For
example, using [ethers.js](https://docs.ethers.org/v6/), you can interact with
Sei's staking module via the staking precompile.

## Setup

To install `ethers` and the Sei EVM bindings, run:

```bash copy
npm install ethers
npm install @sei-js/evm
```

Next, import the staking precompile's address and ABI:

```typescript copy
// Import Solo precompile address and ABI. Solo precompile functions newClaimMsg and newClaimSpecificMsg allows you to create the claim message assets
import { SOLO_PRECOMPILE_ABI, SOLO_PRECOMPILE_ADDRESS } from '@sei-js/evm';
import { hex2uint8, newClaimMsg, newClaimSpecificMsg } from '@sei-js/utils'; 

```

## Using the contract

Set up your provider, signer, and contract instance:

```typescript copy
import { ethers } from 'ethers';
// Using MetaMask as the signer and provider
const provider = new ethers.BrowserProvider(window.ethereum);
await provider.send('eth_requestAccounts', []);
const signer = await provider.getSigner();
const evmAddress = await signer.getAddress();

// Create a contract instance for the solo precompile
const solo = new ethers.Contract(SOLO_PRECOMPILE_ADDRESS, SOLO_PRECOMPILE_ABI, signer);
```

### Claim Assets
To claim assets from the solo precompile, you need to provide your Sei address and EVM address. The `newClaimMsg` function constructs the necessary message for claiming.
```typescript copy
const seiAddress = 'sei1...'; // Replace with your Sei address
const message = await newClaimMsg(seiAddress, evmAddress);
```
To claim CW20/CW721 assets, you can use the `newClaimSpecificMsg` function, which allows you to specify the contract address, type of the asset
```typescript copy 
const cwContractAddress = 'sei1...'; // Replace with your CW20/CW721 contract address
const message = await newClaimSpecificMsg(seiAddress, evmAddress, "CW20", cwContractAddress);    // Replace the type with "CW721" for CW721 assets       
``` 
Build the payload from the message and send the transaction
```typescript copy
const payload = hex2uint8(message)
const tx = await solo.claim(payload, {gasLimit : 150000} ); 
const receipt = await tx.wait(1);
console.log('Claim Completed', receipt);
```


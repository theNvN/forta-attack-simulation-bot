# Attack Simulation Agent

### 🥇 First prize winner in [Forta Bot Development Contest](https://docs.forta.network/en/latest/contest7-forta/https://docs.forta.network/en/latest/contest7-forta/)!

## ⚠️ NOTE ⚠️

I highly recommend putting your own Alchemy/Infura rpc url (`jsonRpcUrl`) in `forta.config.json`. Default rpc url is not fast.

## Description

The agent detects for any suspicious contract creation and if detected, it tries to run a simulation of the attack. It pulls the bytecode of created contract, extracts any possible function selector. Then, it fuzzes calls to contract with random calldatas (having valid function selector). If any large amount of configured token balance is moved, a critical alert is sent.

Following setting variables are available to customize in `./settings.ts`:

- `tornadoFundedAccountsCacheLimit`: Max number of cached tornado funded accounts that can be stored in the cache for later reference.
- `tokenDataToCheckInSimulation`: List of token (native ETH/ERC20/ERC721/ERC1155) addresses with a corresponding threshold which the attack simulation bot will check for balance changes and notify if the balance change/transfer is greater than or equal to the threshold.

## Supported Chains

- Ethereum

## Alerts

Alerts fired by agent:

- TORNADO_CASH_FUNDED_ADDRESS
  - Fired when an address is funded by Tornado Cash
  - Severity is always set to "low"
  - Type is always set to "info"
  - Metadata
    - suspectedAccount: The address that was funded
- SUSPICIOUS_CONTRACT_CREATION
  - Fired when a contract is created by a tornado funded address
  - Severity is always set to "high"
  - Type is always set to "suspicious"
  - Metadata
    - suspiciousSender: The address that created the contract
    - suspiciousContract: The address of the contract that was created
- MALICIOUS_TRANSACTION_SIMULATION
  - Fired when a it is found that a suspected contract can drain high value funds - eth or any token
  - Severity is always set to "critical"
  - Type is always set to "exploit"
  - Metadata
    - attacker: The address that is attacking the contract
    - attackerContract: The address of the contract that is initiating the attack
    - token: The token address that is being drained or value - "native" if Ether is being drained
    - transferValue: The value that is being transferred

## Test Data

The agent behavior can be verified by running the agent through blocks 14684286 - where tornado funding is detected - and 14684300 - where suspicious contract creation is detected leading the bot to trigger simulation of any possible attack transaction. These transactions actually corresponds to the actual Saddle Finance exploit. Simply run:

```
npm run range 14684286..14684300
```

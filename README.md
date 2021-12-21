[Course TOC, code, and resources](https://github.com/smartcontractkit/full-blockchain-solidity-course-py/blob/main/README.md#lesson-12-upgrades)

[brownie-mix/upgrades-mix](https://github.com/brownie-mix/upgrades-mix) - it has all the codes we'll be learning here - setup `$brownie bake upgrades-mix` (which we are not going to do here; instead we'll be building from scratch ourselves)

## Intro

- 3 Upgrade methodes:
  - Not really upgrade, but parameterize
    - simple but not flexible (can't add change/add new logic; can't add new storage)
    - who the admins? (if it's single person, it's a centralized contract; but it can be the governance contract to be the admin)
  - Social YEET / Migration
    - to replace the old contract with a new one
    - truest to blockchain value; easiest to audit
    - different address; difficult to convince users to move.
  - Proxies
    - the truest form of programmatic upgrades
    - but also easiest to screw up in the process

## Proxies

- uses a lot of low level functionality; the main one is **DelegateCall**

- Terminology:

  - The **implementation contract**, which has all our code of our protocol. When we upgrade, we launch a brand new implementation contract
  - The **proxy contract**, which points to which implementation is the "correct" one, and routes everyone's function calls to that contract
  - The user, they make calls to the proxy
  - The admin, which is the user (or group of users/voters) who upgrade to new implementation contracts.

- all the storage variables are going to be stored in the proxy contract, not in the implementation contracts.
- this way when we upgrade to a new logic contract, all my data stay on my proxy contract.

- Gotchas

  - Storage clashes
  - Function selector clashes

- Proxy Patterns/methodologies:
  - **Transparent Proxy Pattern**
    - admins are only allowed to call **admin functions** (which are functions that govern the upgrades); they can't call any functions in the implementation contract.
      users can call implementation contract functions, not admin functions.
  - Universal Upgrade Proxies (UUPS)
    - they put all the logic of upgrading in the implementation itself.
    - it saves gas and the proxy is smaller
  - Diamond/Multi-Facet Proxy
    - allows for multiple implementation contracts (EIP-2535)
    - allows for making more granular upgrades

## Setup

- `$brownie init`

- `contracts/Box.sol`
- `contracts/BoxV2.sol`, has one extra function `increment()`

- `contracts/transparent_proxy/TransparentUpgradeableProxy.sol`
- `contracts/transparent_proxy/ProxyAdmin.sol`
- both contracts copied from [OpenZeppelin's Transparent Upgradable Proxy](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/proxy/transparent/TransparentUpgradeableProxy.sol), but:

  - changed import lines accordingly (from local location to github)
  - add dependencies and remapping in `brownie-config.yaml`

- `brownie compile`

## Box.py, implementation contract

- `scripts/01_deploy_box.py`
- `scripts/helpful_scripts.py`

- test run
  - `$brownie run scripts/01_deploy_box.py`, shows 0
  - if we replace `print(box.retrieve())` with `print(box.increment())`, we will get `AttributeError: Contract 'Box' object has no attribute 'increment'`

**Commit 1**

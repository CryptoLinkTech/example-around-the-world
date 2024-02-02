# HelloHOP Path Following Message Example

`HelloHOP` is an example implementation designed to demonstrate the capabilities of CryptoLink.Tech technology in facilitating cross-chain message passing. This sample contract illustrates the use of the [CryptoLink.Tech NPM package](https://github.com/CryptoLinkTech/npm) for enabling messages to traverse multiple blockchain networks via a predefined sequence of chain IDs. It serves primarily as a technical showcase, highlighting the mechanics of cross-chain communication without external bridges in a multi-chain environment.

## Features

- **Multi-Hop Messages**: Facilitates the passage of messages across multiple blockchain networks in a sequential manner.
- **Cross-Chain Functionality**: Demonstrates the ability to conduct cross-chain interactions using the underlying capabilities of the CryptoLink.Tech framework.
- **CryptoLink.Tech Integration**: Implements the CryptoLink.Tech NPM package, showcasing its utility in cross-chain messaging.

## Prerequisites

Before you begin, ensure you have the following installed:
- Node.js and npm (Node Package Manager)
- A text editor such as VSCode for editing `.sol` and `.ts` files
- GIT installed
- Testnet Tokens 
  - [fantom testnet faucet](https://faucet.fantom.network/) 
  - [polygon testnet faucet](https://faucet.polygon.technology/)
  - [avalanche testnet faucet]()
  - [ethereum holesky testnet faucet]()

Please visit [node documentation link](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm) and the [git install documentation](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) for more information.



## Installation

Please open a terminal to run the following commands. You can use any terminal of your choice, including the built in terminal in vscode (Terminal -> New Terminal)

1. **Clone the Repository**: 
   ```
   git clone https://github.com/CryptoLinkTech/hello-hop.git
   ```

   After cloning the repository, if using vscode or a similar IDE, you can now open the hello-hop folder in your IDE of choice.

2. **Install Dependencies**:
   ```
   npm install
   ```

3. **Set Up Environment Variables**:
   Create a new `.env` file to set your EVM private key for contract deployment or copy and edit the existing `.env.example` to `.env`
    ```
    PRIVATE_KEY=0000000000000000000000000000
    ```

## Deployment

Deploy the HelloHOP contract to your desired networks. This must be done for each network you wish to operate on. You can see a list of our networks in the [NPM package documentation](https://github.com/CryptoLinkTech/npm?tab=readme-ov-file#testnets)

1. **Fantom Testnet Deployment:**

```
npx hardhat --network fantom-testnet deploy
```

2. **Polygon Testnet Deployment:**

```
npx hardhat --network polygon-testnet deploy

```
3. **Avalanche Testnet Deployment:**

```
npx hardhat --network avalanche-testnet deploy
```

4. **Ethereum Holesky Testnet Deployment:**

```
npx hardhat --network ethereum-holesky deploy
```

## Configuration

Edit the `networks.ts` file and include all of the networks the contract is deployed on.

```
const networks = [
    "avalanche-testnet",
    "ethereum-holesky",
    "fantom-testnet",
    "polygon-testnet"
];
export default networks;
```

Once all contracts are deployed across the desired networks and listed in `networks.ts`, configure them using the provided script. Remember, if a new network is added later, all contracts must be reconfigured.

1. **Fantom Testnet Configuration:**

```
npx hardhat --network fantom-testnet configure
```

2. **Polygon Testnet Configuration:**

```
npx hardhat --network polygon-testnet configure
```    

3. **Avalanche Testnet Configuration:**

```
npx hardhat --network avalanche-testnet configure
```

4. **Ethereum Holesky Testnet Configuration:**

```
npx hardhat --network ethereum-holesky configure
```    

## Usage

### Initiating a Message

To start a message off, the `go()` method is called on any of the networks the contract is deployed on, and the path desired is set with the `--path` parameter followed by a comma seperated list of Chain IDs. Chain IDs can be looked up in the [NPM package documentation](https://github.com/CryptoLinkTech/npm?tab=readme-ov-file#testnets).

```
npx hardhat --network fantom-testnet go --path 80001,43113,17000
```

## Contract Breakdown

The `HelloHOP` contract is a Solidity implementation designed for demonstrating cross-chain message passing using the CryptoLink.Tech framework. It allows messages to be sent sequentially across multiple blockchain networks.

### Contract Overview

- **Inheritance**:
  - `MessageClient`: Inherits from the CryptoLink.Tech's `MessageClient` for handling cross-chain messages.
  - `Ownable`: Inherits from OpenZeppelin's `Ownable`, restricting certain functions to the contract owner.

### Events

- **`Go`**: Emitted when the message passing process is initiated.
- **`Completed`**: Emitted when the message has traversed all the specified chains.
- **`NextHop`**: Emitted on each hop to the next chain in the sequence.

### Function: `go`

```solidity
function go(uint[] calldata _chainlist) external onlyOwner
```

- **Purpose**: Initiates the message passing process.
- **Parameters**: `_chainlist` - an array of chain IDs representing the path for the message.
- **Functionality**:
  - Encodes the current chain ID, hop number (starting at 1), and the chain list into a byte array `_data`.
  - Sends the message to the first chain in the list using `_sendMessage`.
  - Triggers the `Go` event.

### Function: `messageProcess`

```solidity
function messageProcess(uint, uint _sourceChainId, address _sender, address, uint, bytes calldata _data) external override onlySelf(_sender, _sourceChainId)
```

- **Purpose**: Handles incoming messages and forwards them if there are more hops to follow.
- **Parameters**:
  - `_sourceChainId`: Chain ID of the message sender.
  - `_sender`: Address of the message sender.
  - `_data`: Encoded data containing the start chain ID, hop count, and chain list.
- **Functionality**:
  - Decodes `_data` to extract the start chain ID, hop count, and chain list.
  - If the current hop is the last one, emits the `Completed` event.
  - Otherwise, increments the hop count, encodes a new message, and sends it to the next chain in the list.
  - Emits the `NextHop` event for each successful hop to the next chain.

### Security and Modifiers

- **`onlyOwner` Modifier (from `Ownable`)**: Restricts the execution of the `go` function to the contract owner.
- **`onlySelf` Modifier**: Ensures that the `messageProcess` function is only callable by the contract itself, as part of the cross-chain message handling.

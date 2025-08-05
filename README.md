# Foundry NFT

[![CI](https://github.com/tohidul3417/foundry-nft/actions/workflows/test.yml/badge.svg)](https://github.com/tohidul3417/foundry-nft/actions/workflows/test.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

This project is a Foundry-based implementation of two NFT contracts: a basic ERC721 and a more advanced "Mood NFT" that demonstrates dynamic on-chain metadata. It was created as part of the "Develop an NFTs Collection" section of the Advanced Foundry course offered by Cyfrin Updraft.

## Architecture

This repository contains two primary NFT smart contracts:

  * **`BasicNft.sol`**: This is a simple ERC721 token that allows for the minting of a single NFT with a provided `tokenURI`. It maintains a counter for the total number of tokens minted.
  * **`MoodNft.sol`**: A more complex ERC721 contract where the NFT's metadata, specifically the SVG image, changes based on the owner's interaction. The contract has a `Mood` enum with `HAPPY` and `SAD` states. The `tokenURI` is dynamically generated on-chain using Base64 encoding of the SVG image and JSON metadata. The SVG data for the happy and sad moods is stored as constant strings within the contract. The `flipMood` function allows the owner of the NFT to change its state, which in turn changes its appearance.

## Getting Started

### Prerequisites

  * [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
  * [Foundry](https://getfoundry.sh/)

### Installation

1.  **Clone the repository** (including submodules):

    ```bash
    git clone --recurse-submodules https://github.com/tohidul3417/foundry-nft.git
    cd foundry-nft
    ```

    The project uses submodules for its dependencies:

      * `@openzeppelin/contracts`
      * `cyfrin/foundry-devops`

2.  **Install dependencies**:

    ```bash
    forge install
    ```

3.  **Build the project**:

    ```bash
    forge build
    ```

4.  **Set up environment variables**:
    Create a `.env` file in the root of the project to hold your RPC URLs and other sensitive information.

    ```bash
    touch .env
    ```

    Add the following variables to your `.env` file, replacing the placeholder values:

    ```
    SEPOLIA_RPC_URL="YOUR_SEPOLIA_RPC_URL"
    PRIVATE_KEY="YOUR_PRIVATE_KEY"
    ETHERSCAN_API_KEY="YOUR_ETHERSCAN_API_KEY"
    ```

### ⚠️ Advanced Security: The Professional Workflow for Key Management

Storing a plain-text `PRIVATE_KEY` in a `.env` file is a significant security risk. If that file is ever accidentally committed to GitHub, shared, or compromised, any funds associated with that key will be stolen instantly.

The professional standard is to **never store a private key in plain text**. Instead, we use Foundry's built-in **keystore** functionality, which encrypts your key with a password you choose.

Here is the clear, step-by-step process:

#### **Step 1: Create Your Encrypted Keystore**

This command generates a new private key and immediately encrypts it, saving it as a secure JSON file.

1.  **Run the creation command:**

    ```bash
    cast wallet new
    ```

2.  **Enter a strong password:**
    The terminal will prompt you to enter and then confirm a strong password. **This is the only thing that can unlock your key.** Store this password in a secure password manager (like 1Password or Bitwarden).

3.  **Secure the output:**
    The command will output your new wallet's **public address** and the **path** to the encrypted JSON file (usually in `~/.foundry/keystores/`).

      * Save the public address. You will need it to send funds to your new secure wallet.
      * Note the filename of the keystore file.

At this point, your private key exists only in its encrypted form. It is no longer in plain text on your machine.

#### **Step 2: Fund Your New Secure Wallet**

Use a faucet or another wallet to send some testnet ETH to the new **public address** you just generated.

#### **Step 3: Use Your Keystore Securely for Deployments**

Now, when you need to send a transaction (like deploying a contract), you will tell Foundry to use your encrypted keystore. Your private key is **never** passed through the command line or stored in a file.

1.  **Construct the command:**
    Use the `--keystore` flag to point to your encrypted file and the `--ask-pass` flag to tell Foundry to securely prompt you for your password.

2.  **Example Deployment Command:**

    ```bash
    forge script script/DeployBasicNft.s.sol:DeployBasicNft \
        --rpc-url $SEPOLIA_RPC_URL \
        --keystore ~/.foundry/keystores/UTC--2025-07-27T...--your-wallet-address.json \
        --ask-pass \
        --broadcast
    ```

3.  **Enter your password when prompted:**
    Foundry will pause and securely ask for the password you created in Step 1.

**The Atomic Security Insight:** When you run this command, Foundry reads the encrypted file, asks for your password in memory, uses it to decrypt the private key for the single purpose of signing the transaction, and then immediately discards the decrypted key. The private key never touches your shell history or any unencrypted files. This is a vastly more secure workflow.

## Usage

### Testing

The project includes a test suite to ensure the correctness of the NFT functionality.

  * **Run all tests**:
    ```bash
    forge test
    ```
  * **Check test coverage**:
    ```bash
    forge coverage
    ```

### Deployment and Interaction

The `script/` directory contains Foundry scripts for deploying the contracts and interacting with the NFTs. The `Makefile` also provides convenient commands for these actions.

  * **Deploy `BasicNft`**:
    ```bash
    make deploy ARGS="--network sepolia"
    ```
  * **Mint `BasicNft`**:
    ```bash
    make mint ARGS="--network sepolia"
    ```
  * **Deploy `MoodNft`**:
    ```bash
    make deployMood ARGS="--network sepolia"
    ```
  * **Mint `MoodNft`**:
    ```bash
    make mintMoodNft ARGS="--network sepolia"
    ```
  * **Flip `MoodNft` mood**:
    ```bash
    make flipMoodNft ARGS="--network sepolia"
    ```

## ⚠️ Security Disclaimer

This project was built for educational purposes and has **not** been audited. Do not use in a production environment or with real funds. Always conduct a full, professional security audit before deploying any smart contracts.

## License

This project is distributed under the MIT License. See `LICENSE` for more information.

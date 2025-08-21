# QBridge - Qubic to Ethereum Cross-Chain Bridge

[![Qubic](https://img.shields.io/badge/Qubic-Bridge-blue.svg)](https://qubic.org)
[![Security](https://img.shields.io/badge/Security-Audited-green.svg)](https://kudelski-security.com)
[![License](https://img.shields.io/badge/License-Vottun%20Proprietary-red.svg)](LICENSE)

## Overview

QBridge is a next-generation, non-custodial cross-chain bridge that enables seamless token transfers between the Qubic blockchain and the Ethereum network. Built with security and decentralization as core principles, QBridge provides a trust-minimized solution for cross-chain interoperability.

> **⚠️ Important Disclaimer:** This proposal covers the smart contract implementation and deployment only. It does **not** include ongoing maintenance/operation costs, nor does it provide initial liquidity for the bridge. These operational aspects would need to be addressed separately by the implementing team or community.

## 🚀 Key Features

### 🔒 **Dual Light-Client Verification (DLV)**
- Each chain runs a lightweight verifier of the other's block headers
- Merkle proof verification for trust-minimized two-way peg
- No centralized custodians required

### 💰 **Mint/Burn Wrapped Assets**
- Native Qubic tokens are locked when transferring to Ethereum
- ERC-20 `wQUBIC` tokens are minted 1-for-1 on Ethereum
- Reverse process burns wrapper tokens and unlocks native assets
- Guarantees constant supply parity across chains

### ⚡ **Gas-Optimized Batch Relays**
- Negligible fees on Qubic side
- Bundled proof transactions on Ethereum to amortize gas costs
- Dramatically reduced user transaction costs

### 🌉 **Cross-Chain Message Bus**
- Support for arbitrary payloads beyond token transfers
- Governance votes, oracle data, and contract calls
- Enables true multi-chain dApp development

### 💧 **Instant Liquidity Pools**
- LP-backed pools for users who can't wait for finality
- "Liquidity vouchers" for instant asset delivery
- LP providers earn small fees (similar to LayerZero/Hop fast lanes)

### 🎯 **Unified Wallet UX**
- One-click bridge integration in Qubic and Ethereum wallets
- Real-time progress tracking on both chains
- Simplified user experience hiding technical complexity

### 🔍 **Open-Source Security**
- All contracts and validator code are open-source
- Third-party security audits by Kudelski Security
- Full transparency with on-chain logging of all transfers and proofs

## 💸 Fee Structure

**Total Fee: 1%** (0.5% per direction)

### Fee Distribution
- **50% for Development Team**
  - 30% kept by dev team
  - 20% locked in liquidity pools

- **50% for Shareholders**
  - 30% kept by shareholders
  - 20% locked in liquidity pools

## 🏗️ Technical Architecture

### Smart Contract Structure

The QBridge smart contract (`VOTTUNBRIDGE`) implements the following core components:

#### Core Data Structures
```cpp
struct BridgeOrder {
    id qubicSender;              // Sender address on Qubic
    id qubicDestination;         // Destination address on Qubic
    Array<uint8, 64> ethAddress; // Destination Ethereum address
    uint64 orderId;              // Unique ID for the order
    uint64 amount;               // Amount to transfer
    uint8 orderType;             // Type of order
    uint8 status;                // Order status
    bit fromQubicToEthereum;     // Transfer direction
    bit tokensReceived;          // Tokens received flag
    bit tokensLocked;            // Tokens locked flag
};
```

#### Key Functions

**Order Management:**
- `createOrder()` - Create new bridge orders
- `getOrder()` - Retrieve order information
- `completeOrder()` - Complete successful transfers
- `refundOrder()` - Refund failed transfers

**Administrative:**
- `setAdmin()` - Set contract administrator
- `addManager()` / `removeManager()` - Manage bridge operators
- `withdrawFees()` - Withdraw accumulated fees

**Liquidity Management:**
- `addLiquidity()` - Add liquidity to the bridge
- `getTotalLockedTokens()` - Check locked token balance
- `getAvailableFees()` - View fee distribution

## 🔧 Installation & Setup

### Prerequisites
- Qubic Core development environment
- Ethereum development tools
- Understanding of cross-chain bridge mechanics

### Contract Deployment

1. **Compile the Smart Contract**
   ```bash
   # Ensure you have the Qubic development environment set up
   # The contract is written in Qubic's C++-like language
   ```

2. **Deploy to Qubic Network**
   ```bash
   # Deploy the VOTTUNBRIDGE contract to Qubic mainnet
   # Ensure proper admin and manager addresses are configured
   ```

3. **Configure Ethereum Side**
   ```bash
   # Deploy corresponding Ethereum contracts
   # Set up light client verification
   # Configure wrapped token contracts
   ```

## 📋 Usage Examples

### Creating a Bridge Order (Qubic to Ethereum)

```cpp
// Example: Transfer 1000 QUBIC to Ethereum
createOrder_input input;
input.ethAddress = "0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b6";
input.amount = 1000;
input.fromQubicToEthereum = true;
input.qubicDestination = NULL_ID; // Not used for Qubic->Ethereum

createOrder_output output;
CALL(createOrder, input, output);

if (output.status == 0) {
    // Order created successfully
    uint64 orderId = output.orderId;
    // Transfer tokens to contract
    transferToContract_input transferInput;
    transferInput.amount = 1000;
    transferInput.orderId = orderId;
    CALL(transferToContract, transferInput, transferOutput);
}
```

### Creating a Bridge Order (Ethereum to Qubic)

```cpp
// Example: Transfer 1000 wQUBIC back to Qubic
createOrder_input input;
input.ethAddress = "0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b6";
input.amount = 1000;
input.fromQubicToEthereum = false;
input.qubicDestination = YOUR_QUBIC_ADDRESS;

createOrder_output output;
CALL(createOrder, input, output);
```

### Checking Order Status

```cpp
getOrder_input input;
input.orderId = 12345;

getOrder_output output;
CALL(getOrder, input, output);

if (output.status == 0) {
    // Order found
    OrderResponse order = output.order;
    // Process order information
}
```

## 🔒 Security Features

### Audit Status
- **Kudelski Security Audit**: Version 2.1 completed
- **Open Source**: All code publicly available for review
- **On-chain Logging**: All operations logged for transparency

### Security Measures
- **Multi-signature Management**: Admin and manager roles
- **Order Validation**: Comprehensive input validation
- **State Management**: Secure token locking/unlocking
- **Error Handling**: Robust error codes and logging
- **Fee Protection**: Secure fee collection and distribution

## 📊 Contract Statistics

### Capacity
- **Order Storage**: 1024 concurrent orders
- **Manager Limit**: 16 authorized managers
- **Fee Precision**: Billionths precision (0.5% = 5,000,000)

### State Variables
- `lockedTokens`: Total tokens locked in bridge
- `totalReceivedTokens`: Total tokens received
- `earnedFees`: Accumulated fees
- `distributedFees`: Fees already distributed

## 🤝 Contributing

We welcome contributions to QBridge! Please follow these guidelines:

1. **Fork the repository**
2. **Create a feature branch**
3. **Make your changes**
4. **Add tests if applicable**
5. **Submit a pull request**

### Development Guidelines
- Follow Qubic smart contract best practices
- Ensure comprehensive error handling
- Add appropriate logging for debugging
- Test thoroughly on testnet before mainnet

## 📄 License

This project is licensed under the Qubic License Agreement by Vottun. All rights reserved. See the [LICENSE](LICENSE) file for complete terms and conditions.

**Note:** This software is developed specifically for the Qubic ecosystem. Commercial use by third parties is not permitted without explicit authorization from Vottun.

## 🔗 Links

- **Qubic Official**: [https://qubic.org](https://qubic.org)
- **Security Audit**: [Kudelski Security Report](Kudelski_Security_Qubic_Secure_Code_Review_2.0.pdf)
- **Technical Proposal**: [QBridge Proposal](2025-08-19-VottunBridge.md)

## 📞 Support

For technical support or questions about QBridge:

- **Documentation**: Check the technical proposal document
- **Issues**: Open an issue in this repository
- **Security**: Report security issues privately to the development team

---

**Disclaimer**: This bridge is experimental software. Use at your own risk and always verify transactions carefully.

---

*Built with ❤️ for the Qubic ecosystem*

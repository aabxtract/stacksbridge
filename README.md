# STX ⇄ ETH Bridge

A decentralized bridge application for seamless asset transfers between Stacks and Ethereum networks.

## Overview

The STX-ETH Bridge enables trustless cross-chain transfers, allowing users to move assets between the Stacks blockchain and Ethereum mainnet while maintaining security and decentralization.

## Features

### Core Functionality
- **Bidirectional Transfers**: Move assets from STX to ETH and back
- **Native Asset Support**: Bridge STX, ETH, and wrapped tokens
- **Fast Finality**: Transfers complete within minutes
- **Trustless Design**: No centralized custody of funds

### User Experience
- **Simple Interface**: Connect wallet and bridge in three clicks
- **Real-time Tracking**: Monitor transfer status across both chains
- **Transaction History**: View all past bridge operations
- **Multi-wallet Support**: Compatible with Leather, Xverse, MetaMask, and more

### Security
- **Smart Contract Verified**: Audited bridge contracts on both chains
- **Threshold Signatures**: Multi-party validation for transfers
- **Proof Verification**: Cryptographic proof of chain state
- **Emergency Pause**: Safety mechanism for critical situations

## How It Works

### STX → ETH Transfer

1. **Lock**: User locks STX on Stacks blockchain
2. **Proof**: Bridge validators generate proof of lock
3. **Mint**: Equivalent wrapped STX minted on Ethereum
4. **Complete**: User receives wSTX on Ethereum

### ETH → STX Transfer

1. **Burn**: User burns wrapped tokens on Ethereum
2. **Proof**: Bridge validators verify burn transaction
3. **Release**: STX unlocked from bridge contract on Stacks
4. **Complete**: User receives STX on Stacks blockchain

## Getting Started

### Prerequisites

**Wallets**
- Stacks wallet (Leather, Xverse, or Hiro)
- Ethereum wallet (MetaMask, WalletConnect compatible)

**Tokens**
- STX for bridging to Ethereum
- ETH for gas fees on Ethereum
- Wrapped STX (wSTX) for bridging back to Stacks

### Installation

```bash
# Clone the repository
git clone https://github.com/your-org/stx-eth-bridge.git
cd stx-eth-bridge

# Install dependencies
npm install

# Configure environment
cp .env.example .env
# Add your RPC endpoints and contract addresses

# Start development server
npm run dev
```

### Configuration

Create a `.env` file with:

```bash
# Stacks Network
NEXT_PUBLIC_STACKS_NETWORK=mainnet
NEXT_PUBLIC_STACKS_API=https://api.mainnet.hiro.so

# Ethereum Network
NEXT_PUBLIC_ETH_NETWORK=mainnet
NEXT_PUBLIC_ETH_RPC=https://eth-mainnet.g.alchemy.com/v2/YOUR_KEY

# Bridge Contracts
NEXT_PUBLIC_STACKS_BRIDGE_CONTRACT=SP...
NEXT_PUBLIC_ETH_BRIDGE_CONTRACT=0x...

# Validator API
NEXT_PUBLIC_VALIDATOR_API=https://validators.bridge.com
```

## Usage

### Bridge STX to Ethereum

```javascript
import { bridgeToEthereum } from '@/lib/bridge';

// Connect wallets
await connectStacksWallet();
await connectEthWallet();

// Initiate bridge transfer
const tx = await bridgeToEthereum({
  amount: '100', // 100 STX
  recipient: '0xYourEthAddress',
  stacksAddress: 'SP...'
});

// Monitor transfer
const status = await getTransferStatus(tx.id);
```

### Bridge from Ethereum to STX

```javascript
import { bridgeToStacks } from '@/lib/bridge';

// Connect wallets
await connectEthWallet();
await connectStacksWallet();

// Initiate bridge transfer
const tx = await bridgeToStacks({
  amount: '100', // 100 wSTX
  recipient: 'SP...',
  ethAddress: '0x...'
});

// Track completion
await waitForTransfer(tx.id);
```

## Smart Contracts

### Stacks Bridge Contract

**Location**: `contracts/stacks/bridge.clar`

**Key Functions**:
- `lock-stx` - Lock STX for bridging
- `unlock-stx` - Release STX after proof verification
- `get-lock-info` - Query lock details

### Ethereum Bridge Contract

**Location**: `contracts/ethereum/Bridge.sol`

**Key Functions**:
- `mint` - Mint wrapped STX on Ethereum
- `burn` - Burn wrapped STX to bridge back
- `verifyProof` - Validate Stacks chain proof

## Fee Structure

| Transfer Type | Network Fee | Bridge Fee | Total Est. |
|--------------|-------------|------------|------------|
| STX → ETH    | ~0.1 STX    | 0.3%       | Variable   |
| ETH → STX    | Gas + ~0.05 ETH | 0.3%   | Variable   |

**Note**: Fees adjust based on network congestion and transaction complexity.

## API Reference

### Transfer Status

```typescript
GET /api/transfers/:id

Response:
{
  id: string;
  status: 'pending' | 'confirmed' | 'completed' | 'failed';
  fromChain: 'stacks' | 'ethereum';
  toChain: 'stacks' | 'ethereum';
  amount: string;
  txHash: string;
  confirmations: number;
  estimatedCompletion: number;
}
```

### Historical Transfers

```typescript
GET /api/transfers?address=SP...

Response:
{
  transfers: Transfer[];
  total: number;

# PumpFun API - Cryptoscan

A TypeScript API wrapper for interacting with PumpFun on Solana blockchain.

**Current Price**: $900
**Contacts**: https://t.me/dan_cryptoscan

What you get:

- Free setup into your hosting.
- Free help to integrate app to your server
- Full access to the codebase and code updates
- Free updates for app, you can request for free updates via access to issues
- Full access to Project Time tracking by developers

## Features

- Create buy/sell transactions for PumpFun tokens
- Get real-time rates and price information
- Fetch coin details
- WebSocket support for real-time updates
- Built-in retry mechanisms and error handling
- Proxy support for requests

## Installation

```bash
npm install pumpfun-api
```

## Quick Start

```typescript
import { PumpFunApi } from 'pumpfun-api';

// Create instance with default settings
const api = new PumpFunApi();

// Or with custom parameters
const api = new PumpFunApi({
  buySlippage: 1,
  sellSlippage: 5,
  // custom connection and request handlers
});
```

## API Methods

### Get Coin Information

```typescript
const coinInfo = await api.getCoin("COIN_ADDRESS");
```

### Get Exchange Rate

```typescript
const rate = await api.getRate({
  from: "sol",
  to: "COIN_ADDRESS",
  amount: 1
});
```

### Create Transaction

```typescript
const transaction = await api.createTransaction({
  to: "DESTINATION_ADDRESS",
  from: "SOURCE_ADDRESS",
  amount: 1,
  walletAddress: "WALLET_ADDRESS",
  slippage: 1
});
```

### Listen to Price Updates

```typescript
api.listenCoinBump("COIN_ADDRESS", (coin) => {
  console.log("New price update:", coin);
});
```

## REST API Endpoints

### Get Coin Information
```http
GET /coin?address={address}
```

**Parameters:**
- `address` (required): Solana address of the coin (string)

**Response:**
```typescript
{
  mint: string;          // Coin address
  name: string;          // Coin name
  symbol: string;        // Coin symbol
  bonding_curve: string; // Bonding curve address
  // ... other coin properties
}
```

### Get Exchange Rate
```http
GET /rate
```

**Parameters:**
- `to` (required): Destination token address or 'sol' (string)
- `from` (optional): Source token address or 'sol' (string, default: 'sol')
- `amount` (optional): Amount to exchange (number, default: 1)
- `network` (optional): Network name (string)
- `slippage` (optional): Slippage percentage (number)

**Response:**
```typescript
{
  network: string;      // Network name (e.g., "solana")
  service: string;      // Service name (e.g., "pumpfun")
  from: string;         // Source token symbol
  to: string;          // Destination token symbol
  symbol: string;      // Trading pair symbol
  fromAddress: string; // Source token address
  toAddress: string;   // Destination token address
  price: number;       // Exchange rate
  priceUSD: number;    // USD price
  liquidityUSD: number;// Liquidity in USD
  amount: number;      // Output amount
  amountUSD: number;   // Output amount in USD
  fee: number;         // Transaction fee
  feeUSD: number;      // Transaction fee in USD
  impact: number;      // Price impact
  routes: any[];       // Available routes
}
```

### Create Transaction
```http
GET /createTransaction
```

**Parameters:**
- `walletAddress` (required): User's wallet address (string)
- `from` (required): Source token address or 'sol' (string)
- `to` (required): Destination token address or 'sol' (string)
- `amount` (required): Amount to exchange (number)
- `slippage` (optional): Slippage percentage (number)
- `payerAddress` (optional): Payer's address (string, defaults to walletAddress)
- `partnerAddress` (optional): Partner's address for fee collection (string)
- `partnerFeePercentage` (optional): Partner fee percentage (number)
- `fee` (optional): Additional fee (number)

**Response:**
```typescript
{
  transaction: string; // Base64 encoded transaction
}
```

### Create New Coin
```http
GET /create
```

**Parameters:**
- `name` (required): Coin name (string)
- `symbol` (required): Coin symbol (string)
- `uri` (required): Metadata URI (string)
- `payerAddress` (required): Payer's wallet address (string)

**Response:**
```typescript
{
  transaction: string; // Base64 encoded transaction
  mint: string;       // New coin's mint address
}
```

## Configuration

Default parameters:
- Buy slippage: 1%
- Sell slippage: 5%
- Network: Solana Mainnet

## Error Handling

The API includes built-in retry mechanisms for network requests and comprehensive error messages. All methods will throw errors with descriptive messages when operations fail.

## WebSocket Support

Real-time updates are available through WebSocket connections, with automatic reconnection handling and error recovery.

## Requirements

- Node.js 14+
- Solana Web3.js
- Active internet connection

## License

ISC

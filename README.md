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

The package includes a Hono-based HTTP server with the following endpoints:

- `GET /coin?address=<address>` - Get coin information
- `GET /rate?from=<from>&to=<to>&amount=<amount>` - Get exchange rate
- `GET /createTransaction` - Create a new transaction

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

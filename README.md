# PumpFun API - Cryptoscan

A TypeScript API wrapper for interacting with PumpFun on Solana blockchain.

**Current Price**: $400
**Contacts**: https://t.me/dan_cryptoscan

What you get:

- Free setup into your hosting.
- Free help to integrate app to your server
- Full access to the codebase and code updates
- Free updates for app, you can request for free updates via access to issues
- Full access to Project Time tracking by developers

## Transaction Example

Here's a complete example of creating and sending a transaction using the API:

```typescript
import { Connection, VersionedTransaction } from "@solana/web3.js";

async function createAndSendTransaction() {
  // Get the transaction from API
  const txn = await fetch(
    "http://localhost:3000/createTransaction?" + new URLSearchParams({
      walletAddress: "6iFSxhMwqctFx41TE2yC7eFPT68PHRgt11FfFqYT2uhJ",
      from: "So11111111111111111111111111111111111111112",
      to: "YOUR_COIN_ADDRESS",  // address of the coin you want to buy
      amount: "1",              // amount in SOL
      slippage: "1"            // slippage in percentage
    })
  )
    .then((res) => res.json())
    .then((data) => data.transaction);

  // Setup connection
  const connection = new Connection(rpcUrl, {
    wsEndpoint: wsRpcUrl,
  });

  // Deserialize the transaction
  const transaction = VersionedTransaction.deserialize(
    Buffer.from(txn, "base64")
  );

  if (transaction instanceof Error) {
    return transaction;
  }

  // Sign with your wallet
  transaction.sign([wallet]);

  // Send the transaction
  return sendTransaction(transaction, { connection });
}
```

## Docker Deployment

### Prerequisites
- Docker installed on your system
- NPM token for private package access

### Build and Run

1. Create `.env` file with your NPM token:
```bash
NPM_TOKEN=your_npm_token_here
```

2. Build the Docker image:
```bash
docker build --build-arg NPM_TOKEN=$NPM_TOKEN -t pumpfun-api .
```

3. Run the container:
```bash
docker run -p 3000:3000 pumpfun-api
```

The API will be available at `http://localhost:3000`

### Docker Compose (Optional)

Create `docker-compose.yml`:
```yaml
version: '3'
services:
  pumpfun-api:
    build:
      context: .
      args:
        - NPM_TOKEN=${NPM_TOKEN}
    ports:
      - "3000:3000"
    restart: unless-stopped
```

Then run:
```bash
docker-compose up -d
```

### Environment Variables
- `NPM_TOKEN` - Required for accessing private npm packages

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
POST /create
Content-Type: multipart/form-data
```

**Parameters (FormData):**
- `file` (required): Image file for the coin
- `name` (required): Coin name (string)
- `symbol` (required): Coin symbol (string)
- `description` (required): Coin description (string)
- `twitter` (optional): Twitter link (string)
- `telegram` (optional): Telegram link (string)
- `website` (optional): Website link (string)
- `showName` (optional): Show name flag (boolean, default: true)
- `video` (optional): Video link (string)
- `payerAddress` (required): Payer's wallet address (string)

**Response:**
```typescript
{
  transaction: string; // Base64 encoded transaction
  mint: string;       // New coin's mint address
}
```

**Example using Node.js:**
```typescript
import { readFileSync } from 'fs';
import FormData from 'form-data';
import fetch from 'node-fetch';

async function createNewCoin() {
  const formData = new FormData();
  
  // Read image file
  const imageBuffer = readFileSync('./coin-image.png');
  formData.append('file', imageBuffer, {
    filename: 'coin-image.png',
    contentType: 'image/png'
  });

  // Add other required fields
  formData.append('name', 'My Coin');
  formData.append('symbol', 'MYCOIN');
  formData.append('description', 'This is my awesome coin');
  formData.append('twitter', 'https://twitter.com/mycoin');
  formData.append('telegram', 'https://t.me/mycoin');
  formData.append('website', 'https://mycoin.com');
  formData.append('showName', 'true');
  formData.append('video', '');
  formData.append('payerAddress', 'YOUR_SOLANA_WALLET_ADDRESS');

  const response = await fetch('http://localhost:3000/create', {
    method: 'POST',
    body: formData
  });

  const result = await response.json();
  console.log('Created coin:', result);
}

createNewCoin().catch(console.error);
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

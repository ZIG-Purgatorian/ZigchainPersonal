# Adding a New ERC20 Asset to ZIGChain

This guide describes the process for adding a new ERC20 token to ZIGChain, enabling users to bridge and transfer ERC20 tokens from Ethereum to ZIGChain.

## Prerequisites

- Ethereum wallet with the ERC20 tokens
- Cosmos Hub wallet
- ZIGChain wallet
- `zigchaind` CLI installed and configured
- Access to the official Skip.go app
- Access to the ZIGChain repository (for running scripts if needed)

## Step-by-Step Process

### 1. Submit Token Request

Submit an internal token request with the following information:
- Your wallet address
- Token contract address
- Token symbol and name

Wait for the tokens to arrive in your Ethereum wallet.

### 2. Launch Skip.go App

Launch the official [Skip.go app](https://go.skip.build) to begin the bridging process.

### 3. Verify Route Availability

Check if a route already exists for the ERC20 token between **Ethereum** and **Cosmos Hub** in Skip.go.

**If route exists:** Skip [Step 4](#4-bridge-via-ibc-eureka-if-no-route-exists) and proceed to [Step 5](#5-bridge-via-skipgo).

**If route does NOT exist:** Proceed to [Step 4](#4-bridge-via-ibc-eureka-if-no-route-exists).

### 4. Bridge via IBC Eureka (If No Route Exists)

> **Note:** Skip this step if a route already exists between Ethereum and Cosmos Hub in Skip.go.

If no route exists in Skip.go between Ethereum and Cosmos Hub:

1. Navigate to the ZIGChain repository:
   ```bash
   cd /path/to/zigchain
   ```

2. Set the following environment variables:
   ```bash
   export PUBLIC_KEY=${PUBLIC_KEY:-""} # Replace with the actual public key
   export PRIVATE_KEY=${PRIVATE_KEY:-""} # Replace with the actual private key
   export CONTRACT_ADDRESS=${CONTRACT_ADDRESS:-"0xFc2d0487A0ae42ae7329a80dc269916A9184cF7C"} # Replace with the actual Eureka contract address
   export AMOUNT=${AMOUNT:-"526"} # Replace with the actual amount of tokens to bridge
   export TOKEN_ADDRESS=${TOKEN_ADDRESS:-"0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48"} # Replace with the actual token address
   export RECEIVER_ADDRESS=${RECEIVER_ADDRESS:-""} # Replace with the actual receiver address
   export RELAYER_FEE=${RELAYER_FEE:-"23474"} # Replace with the actual relayer fee
   export RELAYER_ADDRESS=${RELAYER_ADDRESS:-"0x2d574c3f25EFB98df6340E66a778318FE6592E30"} # Replace with the actual relayer address
   ```

3. Run the IBC Eureka bridging script:
   ```bash
   ./sh/eth_cosmos_via_ibcv2.sh
   ```

   This script will bridge the ERC20 tokens to Cosmos Hub via IBC Eureka.

4. Wait approximately **2 hours** for the Skip.go route to appear between Ethereum and Cosmos Hub.

5. Once the route is available, proceed to [Step 5](#5-bridge-via-skipgo).

### 5. Bridge via Skip.go

If the route is available between Ethereum and Cosmos Hub:

1. Use Skip.go to bridge the ERC20 tokens from your Ethereum wallet to your Cosmos Hub wallet.
2. Wait for the tokens to arrive in your Cosmos Hub wallet.

### 6. Transfer via IBC to ZIGChain

After the tokens land in your Cosmos Hub wallet, execute the manual IBC transfer command to send them to ZIGChain:

```bash
zigchaind tx ibc-transfer transfer transfer \
  <channel-id> \
  <zigchain-wallet-address> \
  <amount><denom> \
  --from <your-key-name> \
  --chain-id <chain-id> \
  --fees <fees> \
  -y
```

**Example:**
```bash
zigchaind tx ibc-transfer transfer transfer \
  channel-4 \
  zig1yourwalletaddress \
  1000000ibc/... \
  --from mykey \
  --chain-id zigchain-1 \
  --fees 100000uzig \
  -y
```

> **Note:** Replace `<channel-id>` with the appropriate IBC channel ID between Cosmos Hub and ZIGChain. For mainnet, this is typically `channel-4`. Verify the correct channel ID before executing.

**Reference:** See the ZIGChain internal documentation for detailed IBC transfer instructions and examples.

### 7. Wait for Skip.go Indexer

Once the tokens reach your ZIGChain wallet:

1. Allow approximately **2 hours** for the Skip.go indexer to detect the new asset.
2. After the indexer processes the asset, the direct route between **Ethereum** and **ZIGChain** should become visible in Skip.go.

### 8. Confirm Route in Skip.go

After the route appears in Skip.go:

1. **Confirm the route** is visible and functional in Skip.go between Ethereum and ZIGChain.
2. **Execute a test transaction** on Skip.go to verify the route works correctly:
   - Bridge a small amount of the ERC20 token from Ethereum to ZIGChain
   - Verify the tokens arrive in your ZIGChain wallet

> ⚠️ **Important:** If the route is not added or not working after the waiting period, reach out to the Cosmos team for assistance.

### 9. Override Configuration (If Required)

If any override configuration is required for the asset (e.g., to ensure the asset appears with the correct symbol in Skip.go instead of a wrapped version), follow these steps to add it to the Skip.go registry:

1. **Clone the Skip.go registry repository:**
   ```bash
   git clone https://github.com/skip-mev/skip-go-registry
   cd skip-go-registry
   ```

2. **Create or update the asset list file:**
   
   Create a new file `chains/zigchain-1/assetlist.json` if it does not exist yet, or update the existing file.

3. **Add the asset configuration:**
   
   Add the following content to the `assetlist.json` file (adjust the values for your specific asset):
   
   ```json
   {
       "$schema": "../../assetlist.schema.json",
       "chain_name": "zigchain",
       "assets": [
           {
               "asset_type": "cosmos",
               "name": "ETH",
               "symbol": "ETH",
               "denom": "ibc/5E970F9FF7B696B42F75B92CC05A74B9C44AB0B5D231FBE794A29D74344B19D7",
               "recommended_symbol": "ETH",
               "coingecko_id": "ethereum"
           }
       ]
   }
   ```
   
   > **Note:** Replace the values with your specific asset information:
   > - `name`: Asset name
   > - `symbol`: Asset symbol
   > - `denom`: The IBC denom on ZIGChain
   > - `recommended_symbol`: The symbol to display in Skip.go
   > - `coingecko_id`: CoinGecko identifier (if available)
   
   This configuration ensures that the asset on ZIGChain appears with the correct symbol (e.g., "ETH" instead of "WETH") in the Skip.go app.

4. **Commit and push your changes:**
   ```bash
   git add chains/zigchain-1/assetlist.json
   git commit -m "Add asset override for [ASSET_NAME] on ZIGChain"
   git push origin <your-branch-name>
   ```

5. **Create a Pull Request:**
   - Push your changes to your fork
   - Create a Pull Request to the main repository: https://github.com/skip-mev/skip-go-registry

6. **Fix validation issues and get approval:**
   - Address any validation errors that may appear
   - Request review from the Skip team
   - Wait for approval and merge

7. **Verify changes are applied:**
   - Wait a couple of hours after the PR is merged
   - Check the Skip.go app to ensure the changes are applied and the asset displays correctly

**Reference:** [Skip.go Registry Repository](https://github.com/skip-mev/skip-go-registry)

### 10. Add to Chain Registry

Add the asset to the Cosmos chain registry. This requires adding the asset to **three** chain configuration files:
1. **Origin chain** (Ethereum) - base asset definition
2. **Cosmos Hub** - IBC bridged version via Eureka
3. **ZIGChain** - final destination with full IBC trace path

**Steps:**

1. **Fork the repository:**
   ```bash
   git clone https://github.com/cosmos/chain-registry
   cd chain-registry
   ```

2. **Add asset logo image (if available):**
   - Add the token logo PNG/SVG to: `_non-cosmos/ethereum/images/<token-symbol-lowercase>.png`
   - Ensure the image follows the repository's image guidelines

3. **Add to Ethereum assetlist.json:**
   
   Edit `_non-cosmos/ethereum/assetlist.json` and add the base asset definition:
   
   ```json
   {
     "description": "Token description",
     "type_asset": "erc20",
     "address": "0x...",
     "denom_units": [
       {
         "denom": "0x...",
         "exponent": 0
       },
       {
         "denom": "<symbol-lowercase>",
         "exponent": <decimals>
       }
     ],
     "base": "0x...",
     "name": "Token Name",
     "display": "<symbol-lowercase>",
     "symbol": "SYMBOL",
     "keywords": ["optional", "keywords"],
     "images": [
       {
         "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/_non-cosmos/ethereum/images/<symbol-lowercase>.png"
       }
     ],
     "logo_URIs": {
       "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/_non-cosmos/ethereum/images/<symbol-lowercase>.png"
     }
   }
   ```

4. **Add to Cosmos Hub assetlist.json:**
   
   Edit `cosmoshub/assetlist.json` and add the IBC bridged version:
   
   ```json
   {
     "description": "Token Name bridged via Eureka on Cosmos Hub.",
     "denom_units": [
       {
         "denom": "ibc/<IBC_DENOM_ON_COSMOSHUB>",
         "exponent": 0
       },
       {
         "denom": "<symbol-lowercase>",
         "exponent": <decimals>
       }
     ],
     "type_asset": "ics20",
     "base": "ibc/<IBC_DENOM_ON_COSMOSHUB>",
     "name": "Eureka Bridged Token Name (SYMBOL)",
     "display": "<symbol-lowercase>",
     "symbol": "SYMBOL",
     "traces": [
       {
         "type": "ibc-bridge",
         "counterparty": {
           "chain_name": "ethereum",
           "base_denom": "0x...",
           "channel_id": "channel-0"
         },
         "chain": {
           "channel_id": "08-wasm-1369",
           "path": "transfer/08-wasm-1369/0x..."
         },
         "provider": "Eureka"
       }
     ],
     "logo_URIs": {
       "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/_non-cosmos/ethereum/images/<symbol-lowercase>.png"
     },
     "images": [
       {
         "image_sync": {
           "chain_name": "ethereum",
           "base_denom": "0x..."
         },
         "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/_non-cosmos/ethereum/images/<symbol-lowercase>.png"
       }
     ]
   }
   ```

5. **Add to ZIGChain assetlist.json:**
   
   Edit `zigchain/assetlist.json` and add the final destination with full IBC trace:
   
   ```json
   {
     "description": "Token Name on ZIGChain",
     "denom_units": [
       {
         "denom": "ibc/<IBC_DENOM_ON_ZIGCHAIN>",
         "exponent": 0,
         "aliases": ["optional", "aliases"]
       },
       {
         "denom": "<symbol-lowercase>",
         "exponent": <decimals>
       }
     ],
     "type_asset": "ics20",
     "base": "ibc/<IBC_DENOM_ON_ZIGCHAIN>",
     "name": "Token Name on ZIGChain",
     "display": "<symbol-lowercase>",
     "symbol": "SYMBOL",
     "traces": [
       {
         "type": "ibc-bridge",
         "counterparty": {
           "chain_name": "ethereum",
           "base_denom": "0x...",
           "channel_id": "channel-0"
         },
         "chain": {
           "channel_id": "08-wasm-1369",
           "path": "transfer/08-wasm-1369/0x..."
         },
         "provider": "Eureka"
       },
       {
         "type": "ibc",
         "counterparty": {
           "chain_name": "cosmoshub",
           "base_denom": "ibc/<IBC_DENOM_ON_COSMOSHUB>",
           "channel_id": "channel-1555"
         },
         "chain": {
           "channel_id": "channel-4",
           "path": "transfer/channel-4/transfer/08-wasm-1369/0x..."
         }
       }
     ],
     "logo_URIs": {
       "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/_non-cosmos/ethereum/images/<symbol-lowercase>.png"
     },
     "images": [
       {
         "image_sync": {
           "chain_name": "ethereum",
           "base_denom": "0x..."
         },
         "png": "https://raw.githubusercontent.com/cosmos/chain-registry/master/_non-cosmos/ethereum/images/<symbol-lowercase>.png"
       }
     ]
   }
   ```

6. **Create a pull request:**
   ```bash
   git checkout -b add-<token-symbol>-to-chain-registry
   git add _non-cosmos/ethereum/assetlist.json
   git add _non-cosmos/ethereum/images/<token-symbol>.png
   git add cosmoshub/assetlist.json
   git add zigchain/assetlist.json
   git commit -m "Add <Token Name> (SYMBOL) to chain registry"
   git push origin add-<token-symbol>-to-chain-registry
   ```
   
   Then create a Pull Request to: https://github.com/cosmos/chain-registry

**Required Information:**
- **Token contract address** (Ethereum): `0x...` format
- **Token symbol and name**: Official token name and symbol
- **IBC denom on Cosmos Hub**: The IBC denom after bridging via Eureka (format: `ibc/...`)
- **IBC denom on ZIGChain**: The IBC denom on ZIGChain after IBC transfer (format: `ibc/...`)
- **Decimals/Exponent**: Token decimals (typically 6, 8, 9, or 18)
- **IBC channel information**:
  - Ethereum ↔ Cosmos Hub: `08-wasm-1369` (Eureka bridge)
  - Cosmos Hub ↔ ZIGChain: `channel-1555` (Cosmos Hub side), `channel-4` (ZIGChain side)
- **Logo/image**: PNG or SVG file for the token logo
- **Description**: Brief description of the token

**Important Notes:**
- The IBC denom values can be found by querying your wallet after the tokens arrive
- Ensure all JSON is properly formatted and valid
- The trace structure shows the full path: Ethereum → (Eureka bridge) → Cosmos Hub → (IBC) → ZIGChain
- Channel IDs may vary by network (mainnet vs testnet) - verify the correct channel IDs for your network

**Example PR:** See [cosmos/chain-registry#6744](https://github.com/cosmos/chain-registry/pull/6744) for a complete example including ETH, NEIRO, WBTC, and USDT.

### 11. Whitelist Asset in ZIGChain Hub

Request the asset be whitelisted in the ZIGChain Hub UI to enable full functionality. Provide the following information:
- Token contract address (Ethereum)
- Token symbol and name
- Denom on ZIGChain
- IBC channel information
- Any other required metadata (logo, decimals, etc.)

## Troubleshooting

### Route Not Appearing in Skip.go

- Wait the full 2 hours for indexer processing
- Verify tokens are in your ZIGChain wallet
- Check IBC channel status
- Contact Cosmos team if route still doesn't appear after extended wait

### IBC Transfer Fails

- Verify channel ID is correct
- Check wallet has sufficient fees
- Ensure tokens are in correct format (denom)
- Verify IBC channel is open and active

## Additional Resources

- [Skip.go Documentation](https://docs.skip.build)
- [Cosmos Chain Registry](https://github.com/cosmos/chain-registry)
- [IBC Documentation](https://ibc.cosmos.network/)
- [ZIGChain Documentation](https://docs.zigchain.com/)

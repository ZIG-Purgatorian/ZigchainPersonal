# FAQs from Exchanges

This document provides essential information about ZIGChain for exchange integration and support.

### **Q1: Website Address**

**A:** https://zigchain.com/

### **Q2: Digital Coin Name and Abbreviation**

**A:** ZIGcoin / ZIG

### **Q3: Block Explorer Address**

**A:** https://app.range.org/zig/general

### **Q4: GitHub Address**

**A:** https://github.com/ZIGChain

**Note:** This is a private repository at this moment and requires requesting access.

### **Q5: Node Installation Documents**

**A:** https://docs.zigchain.com/nodes_validators/setup-node

### **Q6: Recommended Hardware Requirements**

**A:**

- **Core Processor:** 8-core (4 physical core), x86_64 architecture
- **Memory:** 32 GB RAM
- **Storage:** 1 TB SSD
- **Network:** Stable internet connection

### **Q7: Block Data of Mainnet or Snapshots**

**A:** We can provide snapshot URL but it is dynamic and not on a website accessible yet.

### **Q8: Block Producing Rate (Average Interval Time of Each Block)**

**A:** Around 3.12 seconds

**Calculation Command:**

```bash
RPC="http://localhost:26657"

# get latest block height
LATEST=$(curl -s $RPC/status | jq -r '.result.sync_info.latest_block_height')

# pick an older block (e.g. 100 blocks before)
EARLIER=$((LATEST - 100))

# get timestamps
T1=$(curl -s "$RPC/block?height=$LATEST" | jq -r '.result.block.header.time')
T0=$(curl -s "$RPC/block?height=$EARLIER" | jq -r '.result.block.header.time')

# convert to epoch seconds
S1=$(date -d "$T1" +%s)
S0=$(date -d "$T0" +%s)

# calculate average block time
echo "scale=3; ($S1 - $S0) / ($LATEST - $EARLIER)" | bc
```

### **Q9: Precision of Each Transaction**

**A:** Precision is with integers only. The smallest unit of ZIG is `uzig` which is 6 decimal places.

### **Q10: Public API Interface URL**

**A:**

**Mainnet:**

- **API:** https://public-zigchain-lcd.numia.xyz
- **RPC:** https://public-zigchain-rpc.numia.xyz

**Testnet:**

- **API:** https://public-zigchain-testnet-lcd.numia.xyz
- **RPC:** https://public-zigchain-testnet-rpc.numia.xyz

### **Q11: Account Memo Function Support**

**A:** Memo is supported.

### **Q12: Creating Account Documents**

**A:** https://docs.zigchain.com/general/accounts

### **Q13: Configuration File Directory**

**A:** Default data directory: `~/.zigchain`

**Directory Structure:**

```
~/.zigchain
├── config
│   ├── app.toml
│   ├── client.toml
│   ├── config.toml
│   ├── genesis.json
│   ├── node_key.json
│   └── priv_validator_key.json
└── data
    └── priv_validator_state.json
```

### **Q14: How to Customize RPC Port and Block Data Directory**

**A:**

**Customize Port:**

Configure through the following files:

- `~/.zigchain/config/config.toml`
- `~/.zigchain/config/app.toml`

**Customize Home Directory:**

To customize the home directory of the blockchain data when running, specify under `$PATH` application directory:

```bash
zigchaind init mynode --home $PATH
```

### **Q15: RPC/SDK Documents Link**

**A:** https://docs.zigchain.com/zigchain-sdk/introduction

### **Q16: API Instructions**

**A:** Common to any Cosmos blockchain. Check Swagger documentation: https://testnet-api.zigchain.com/

### 16.1 Regular Explanation of Accounts or Validating Accounts API

**Endpoint:** `/cosmos/auth/v1beta1/accounts/{account}`

**Example Request:**  
https://testnet-api.zigchain.com/cosmos/auth/v1beta1/accounts/zig1jwgjeaxct84g97nea7vdfuuwvctzvee03x0xaw

### 16.2 Access the Best Height API

**Endpoint:** `/cosmos/base/node/v1beta1/status`

**Example Request:**  
https://testnet-api.zigchain.com/cosmos/base/node/v1beta1/status

### 16.3 Access the Account History API

**Endpoint:** `/cosmos/tx/v1beta1/txs?events=coin_received.receiver='{account}'&pagination.limit=100`

**Example Request:**  
https://testnet-api.zigchain.com/cosmos/tx/v1beta1/txs?events=coin_received.receiver='zig1urape39hlscx2ed7vp5ck6lwa426lu97fx7fun'&pagination.limit=100

### 16.4 Access the Detailed Information of a Trade

**Endpoint:** `/cosmos/tx/v1beta1/txs/{hash}`

**Example Request:**  
https://testnet-api.zigchain.com/cosmos/tx/v1beta1/txs/DD82C55A81FCA848EE245944512B3688DD41E9F41CE1F5136988FBE076FA2189

### 16.5 How to Determine a Trade is Accurate to Avoid Cheating Recharge

- Check successful status code (0 = success)
- Ensure that confirmation is done
- It is possible to verify amount on destination wallet

### 16.6 Transfer Process: Sign Transaction Offline and Broadcast it Online

**Example: Create the unsigned transaction for governance voting**

```bash
zigchaind tx gov vote $VOTE_ID yes \
--from $ACCOUNT \
--chain-id zigchain-1 \
--node https://public-zigchain-testnet-rpc.numia.xyz/ \
--gas auto \
--gas=250000 \
--fees=1000000uzig \
--generate-only \
-o json \
> ~/output.file
```

**Query the transaction:**

`TX_ID` is obtained from the previous request message.

```bash
zigchaind q tx $TX_ID --node https://public-zigchain-testnet-rpc.numia.xyz/
```

**Broadcast the transaction:**

```bash
zigchaind tx broadcast ~/output.file --chain-id zigchain-1 --node https://public-zigchain-testnet-rpc.numia.xyz/
```

### 16.7 Access the Balance of an Account

**Endpoint:** `/cosmos/bank/v1beta1/balances/{account}`

**Example Request:**  
https://testnet-api.zigchain.com/cosmos/bank/v1beta1/balances/zig1urape39hlscx2ed7vp5ck6lwa426lu97fx7fun

### **Q17: Preventive Measures to Avoid Chain's Forking**

**A:** Cosmos uses Comet BFT (= Tendermint BFT) that has instant finality.

### **Q18: Suggestions of Restoring and Recovering Accounts**

**A:**

- Use 12-24-word mnemonic backups
- Multi-sig for high-value accounts
- Recovery via:

```bash
zigchaind keys add --recover
```

### **Q19: The Coin Symbol**

**A:** ZIG

### **Q20: Officially Recognized Wallets**

**A:**

- Keplr Wallet
- Leap Wallet

### **Q21: Cross-Chain Bridge Audit Report**

**A:** ZIG exists on different networks:

| Network   | Contract Address                                  |
| --------- | ------------------------------------------------- |
| Ethereum  | `0xb2617246d0c6c0087f18703d576831899ca94f01`      |
| BEP20     | `0x8C907e0a72C3d55627E853f4ec6a96b0C8771145`      |
| Solana    | `26f12PmBk77wQV1TzLe8XKkNBvMFggbuypxdtMLzNLzz`    |
| Polygon   | `0x7bebd226154e865954a87650faefa8f485d36081`      |
| Injective | `peggy0xb2617246d0c6c0087f18703d576831899ca94f01` |

### **Q22: POW Mode and 51% Attack Prevention**

**A:** ZIGChain is a PoS (Proof of Stake) based chain, not POW.

### **Q23: Most Common Types of Transfers**

**A:** Common to Cosmos blockchains:

- `MsgSend`
- `MsgMultiSend`
- `MsgDelegate`
- `MsgUndelegate`

### **Q24: Transaction Rollback**

**A:** Cosmos has instant finality. There are no rollbacks.

### **Q25: Transaction Timeout Mechanism in TX Pool**

**A:** Common to Cosmos blockchains:

- Transaction timeout mechanism through Mempool configurable in the `config.toml` file
- Additionally, transfers can specify a `--timeout-height` flag

### **Q26: Rent or Reservation**

**A:** Common to Cosmos blockchains. There is no rent or reservation.

### **Q27: UTXO Mode Transaction Failure Determination**

**A:** Common to Cosmos blockchains. ZIGChain uses account model, not UTXO.

### **Q28: Time to Start Mainnet Transfer**

**A:** Mainnet transfer is already open.

### **Q29: Wallet Node Port Exposure**

**A:** Light nodes require only P2P connection with the other nodes, usually through port 26656. NAT IP is acceptable.

### **Q30: Address Rules**

**A:** ZIGChain uses Bech32 prefixes for account and validator addresses:

- **Account prefix:** `zig1`

### **Q31: Node IP Whitelist Requirement**

**A:** No whitelist is needed for synchronization.

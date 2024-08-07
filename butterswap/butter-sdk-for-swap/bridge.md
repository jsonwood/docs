# Bridge

utter Bridge allows bridging supported tokens from one blockchain to another.

### Request[​](https://docs.butternetwork.io/SDK/bridge#request) <a href="#request" id="request"></a>

```javascript
export type BridgeRequestParam = {
  fromAddress: string; // from address
  fromToken: BaseCurrency; // token to be bridged
  fromChainId: string; // from chain id
  toChainId: string; // to chain id
  toAddress: string; // destination chain receiving address
  amount: string; // amount to bridge in minimal uint
  options: ButterTransactionOption; // options
};
```

Where `ButterTransactionOption` contains all the necessary information required to complete a bridge transaction:

```javascript
export type ButterTransactionOption = {
  signerOrProvider?: Signer | Provider | Eth; // When source chain is EVM provide Ethers.js Signer/Provider or Web3.js Eth info
  nearProvider?: NearProviderType; // mandatory when src chain is near
  gas?: string;
  gasPrice?: string;
};

// when send transaction from Near Protocol
type NearProviderType = NearNetworkConfig | WalletConnection;
```

`signerOrProvider`: Butter supports both _ethers.js_ and _web3.js_. If you are using ethers.js, provider the [`Signer`](https://docs.ethers.org/v5/api/signer/) object. If your application choose to use web3.js, please provide [`Eth`](https://web3js.readthedocs.io/en/v1.2.11/web3-eth.html) object in order to send a transaction.

`nearProvider`: Whenever send a transaction from Near Protocol, you have to provide [`NearNetworkConfig`](https://near.github.io/near-api-js/interfaces/connect.ConnectConfig) with keystore provided or [`WalletConnection`](https://near.github.io/near-api-js/classes/walletAccount.WalletConnection/) object

### Response[​](https://docs.butternetwork.io/SDK/bridge#response) <a href="#response" id="response"></a>

Please refer to [response type](https://docs.butternetwork.io/SDK/types#buttertransactionresponse).

### Gas estimation[​](https://docs.butternetwork.io/SDK/bridge#gas-estimation) <a href="#gas-estimation" id="gas-estimation"></a>

Estimate the gas cost for bridge action.

```javascript
async function gasEstimateBridgeToken({
    fromAddress,
    fromToken,
    fromChainId,
    toChainId,
    toAddress,
    amount,
    options,
}: BridgeRequestParam): Promise<string>; // estimated gas in string
```

### Execute Bridge[​](https://docs.butternetwork.io/SDK/bridge#execute-bridge) <a href="#execute-bridge" id="execute-bridge"></a>

```javascript
async function bridgeToken({
    fromAddress,
    fromToken,
    fromChainId,
    toChainId,
    toAddress, // recipient address
    amount, // amount of 'fromToken' to bridge
    options
}: BridgeRequestParam): Promise<ButterTransactionResponse> {};
```

**Example1: Bridge 1 USDC from Ethereum Mainnet to BSC using web3.js**[**​**](https://docs.butternetwork.io/SDK/bridge#example1-bridge-1-usdc-from-ethereum-mainnet-to-bsc-using-web3js)

```javascript
// initiate ButterBridge Class
import {ButterTransactionResponse} from "./responseTypes";
import {PromiEvent} from "web3-core";

// create a Butter bridge instance.
const bridge: ButterBridge = new ButterBridge();

// assemble bridge request parameters
const bridgeRequest: BridgeRequestParam = {
    fromToken: ETH_MAINNET_USDC,
    fromChainId: ChainId.ETH_MAINNET,
    toChainId: ChainId.BSC_MAINNET,
    toAddress: '0x...',
    amount: '1000000',
    options: {
        signerOrProvider: web3.eth, // here we use web3.js as example
    },
};

const response: ButterTransactionResponse = await bridge.bridgeToken(
    bridgeRequest
);

const promiReceipt: PromiEvent<TransactionReceipt> = response.promiReceipt!;

await promiReceipt
    .on('transactionHash', function (hash: string) {
        console.log('hash', hash);
    })
    .on('receipt', function (receipt: any) {
        console.log('receipt', receipt);
    });
```

**Output:**[**​**](https://docs.butternetwork.io/SDK/bridge#output)

```json
hash 0x..... // transaction hash
receipt { // web3.js TransactionReceipt
    status: boolean;
    transactionHash: string;
    transactionIndex: number;
    blockHash: string;
    blockNumber: number;
    from: string;
    to: string;
    contractAddress?: string;
    cumulativeGasUsed: number;
    gasUsed: number;
    effectiveGasPrice: number;
    logs: Log[]..;
    logsBloom: string;
    events?: {
        [eventName: string]: EventLog;
    };
}
```

**Example2: Bridge 1 USDC from Ethereum Mainnet to BSC using ethers.js**[**​**](https://docs.butternetwork.io/SDK/bridge#example2-bridge-1-usdc-from-ethereum-mainnet-to-bsc-using-ethersjs)

```javascript
// initiate ButterBridge Class
import {ButterTransactionReceipt, ButterTransactionResponse} from "./responseTypes";
import {PromiEvent} from "web3-core";

const bridge: ButterBridge = new ButterBridge();

// assemble bridge request parameters
const bridgeRequest: BridgeRequestParam = {
    fromToken: ETH_MAINNET_USDC,
    fromChainId: ChainId.ETH_MAINNET,
    toChainId: ChainId.BSC_MAINNET,
    toAddress: '0x...',
    amount: '1000000',
    options: {
        signerOrProvider: ethers.signer, // here we use ethers.js as example
    },
};

const response: ButterTransactionResponse = await bridge.bridgeToken(
    bridgeRequest
);
console.log("transaction hash", response.hash!)

const receipt: ButterTransactionReceipt = await response.wait!();
console.log('receipt', receipt)
```

**Output:**[**​**](https://docs.butternetwork.io/SDK/bridge#output-1)

```json
transaction hash 0x..... 
receipt {
  to: string;
  from: string;
  gasUsed: string;
  transactionHash: string;
  blockHash?: string;
  blockNumber?: number;
  success?: boolean; // 1 success, 0 failed
}
```

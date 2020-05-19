# For Math Wallet DAPP Developer

## Using Math Wallet Near JS API

### Wallet Api

```
class MathWalletSigner {
  async getPublicKey(accountId, networkId) {
  }
  async signMessage(message, accountId, networkId) {
  }
}
class NearWalletApi {
  constructor(){
    this.signer = new MathWalletSigner();
  }
  // fields = { publicKey, contractId }
  async login(fields = {}){
  }
  async logout() {
  }
}
window.nearWalletApi = new NearWalletApi();
```

### Samples

```
// login
// returns { "name": "cc1", "accountId": "big.betanet", "publicKey":"ed25519:92xqjEF5bpUdHNcTwmoCRucsbHAtciAyyPDtZqr42T8B","permission":"FullAccess","network":"testnet"}
const account = await window.nearWalletApi.login();

// logout
// returns {}
await window.nearWalletApi.logout();

// Sign Transaction
// Use MathWallet Signer
import * as nearApi from 'near-api-js';
const [hash, signedTransaction] = await nearApi.transactions.signTransaction(
        transaction,
        window.nearWalletApi.signer,
        accountId,
        "test"
        );
```

For details, please find the sample in this repo.

### Download Math Wallet 麦子钱包下载

[http://mathwallet.org](http://mathwallet.org)



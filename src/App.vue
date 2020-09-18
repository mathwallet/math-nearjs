<template>
  <div id="app">
    <h4>{{account?JSON.stringify(account):""}}</h4>
    <button @click="login">login</button>
    <button @click="logout">logout</button>
    <br />
    <template v-if="account">
      <h4>{{txhash}}</h4>
      <button @click="sendTransaction">Send Transaction</button>
    </template>
  </div>
</template>

<script>
import * as nearApi from "near-api-js";
import CryptoUtils from "./utils/CryptoUtils";
import BN from "bn.js";

export default {
  name: "App",
  data() {
    return {
      account: null,
      txhash: null,
    };
  },
  methods: {
    async sendTransaction() {
      const provider = new nearApi.providers.JsonRpcProvider(
        "https://rpc.mainnet.nearprotocol.com"
      );
      // Account
      const accountId = this.account.accountId;
      const publicKey = this.account.publicKey;
      const receiverId = "mathwallet.near";

      // RPC
      const accessKeyResponse = await provider.query(
        `access_key/${accountId}/${publicKey}`,
        ""
      );

      const blockHash = nearApi.utils.serialize.base_decode(
        accessKeyResponse.block_hash
      );
      const nonce = accessKeyResponse.nonce;

      // Transaction
      const actions = [
        nearApi.transactions.transfer(new BN("10000000000000000000000")), // Transfer(1 NEAR)
        // nearApi.transactions.createAccount(),                              // Create Account
        // nearApi.transactions.addKey(publicKey,accessKey),                  // Add Key
        // nearApi.transactions.deleteKey(publicKey),                         // Delete Key
        // nearApi.transactions.deployContract(code),                         // Deploy Contract
        // nearApi.transactions.deleteAccount("account.betanet"),             // Delete Account
      ];
      const transaction = nearApi.transactions.createTransaction(
        accountId,
        nearApi.utils.PublicKey.fromString(publicKey),
        receiverId,
        nonce + 1,
        actions,
        blockHash
      );
      const [
        hash,
        signedTransaction,
      ] = await nearApi.transactions.signTransaction(
        transaction,
        window.nearWalletApi.signer,
        accountId
      );

      // Send
      const executionOutcome = await provider.sendTransaction(
        signedTransaction
      );
      this.txhash = JSON.stringify(executionOutcome.transaction_outcome.id);
    },
    login() {
      window.nearWalletApi.login({}).then((account) => {
        this.account = account;
      });
    },
    logout() {
      window.nearWalletApi.logout().then(() => {
        this.account = null;
      });
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

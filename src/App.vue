<template>
  <div id="app">
    <router-view />
  </div>
</template>

<script>
import Web3 from 'web3';
import contracts from './blockchain/contracts';

export default {
  data() {
    return {
      web3: null,
      // contracts: [],
    }
  },
  async created() {
    const isMetamask = await this.checkMetamaskConnection();
    if (isMetamask) {
      const contract = new this.web3.eth.Contract(contracts[0].abi, contracts[0].address);
      console.log(contract);
    }
    
  },
  methods: {
    async checkMetamaskConnection () {
      // Modern dapp browsers
      if (window.ethereum) {
        const { ethereum } = window;
        this.web3 = new Web3(ethereum);

        try {
          // Request account access if needed
          await ethereum.enable();
          // Accounts now exposed
          return true;
        } catch (error) {
          // User denied account access
          return false;
        }
        // ethereum.enable().then((account) => {
        //   const defaultAccount = account[0];
        //   web3Provider.eth.defaultAccount = defaultAccount;

        //   this.web3Provider = web3Provider;
        // });
      }
      // Legacy dapp browsers...
      else if (window.web3) {
        this.web3 = new Web3(window.web3.currentProvider);
        // Accounts always exposed

        return true;
      }
      // Non-dapp browsers...
      else {
        console.log('Non-ethereum browser detected. You should consider trying MetaMask!');
        return false;
      }
    },
  }
}
</script>

<style>
body {
  padding: 0;
  margin: 0;
}

* {
  box-sizing: content-box;
}
</style>
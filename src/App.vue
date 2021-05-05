<template lang="pug">
  #app
    div(v-konami @konami="showApeTax = !showApeTax")
    div(v-if="showApeTax")
      div.apeTax
    div(v-else)
      div(v-if="isDrizzleInitialized && chainId", id="app")
        .chain.is-size-5.is-size-6-mobile {{ chainName }}
        .section
          Section(:config="config" :allConfig="allConfig" :chainId="chainId" :chainCoin="chainCoin" :chainExplorer="chainExplorer")
      div(v-else)
        div Loading Humanitarian Vaults 💧...
</template>

<script>
import config from './config.js'
import chains from './chains.json'
import Vault from './Vault'
import Home from './Home'
import NotFound from './NotFound'
import { mapGetters } from 'vuex'
import Web3 from 'web3';

const vaultPath = window.location.pathname.substring(1)
const vaultConfig = config[vaultPath] || null;

let VaultType;

switch (window.location.pathname) {
  default:
    VaultType = Object.prototype.hasOwnProperty.call(config, vaultPath) ? Vault : NotFound;
}

const Section = window.location.pathname === '/' ? Home : VaultType

let web3 = new Web3(Web3.givenProvider);
window.ethereum.on("chainChanged", (chainIdHex) =>
  window.location.reload()
);


export default {
  name: 'app',
  components: {
    Section,
  },
  data() {
    return {
      config: vaultConfig,
      allConfig: config,
      isHome: window.location.pathname === '/',
      showApeTax: false,
    }
  },
  asyncComputed: {
    ...mapGetters('drizzle', ['isDrizzleInitialized', 'drizzleInstance']),
    chainId() {
      if (this.isDrizzleInitialized) {
        return this.drizzleInstance.web3.eth.getChainId();
      }
      return 0
    },
    chainName() {
      if (this.chainId) {
        return chains[this.chainId].name;
      }
    },
    chainCoin() {
      if (this.chainId) {
        return chains[this.chainId].coin;
      }
    },
    chainExplorer() {
      if (this.chainId) {
        return chains[this.chainId].block_explorer;
      }
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css?family=Open+Sans:300,300italic,400,400italic,600,600italic,700,700italic,800,800italic%7COpen+Sans+Condensed:300,300italic,700');

#app {
  font-family: 'Open Sans', sans-serif;
  font-size: 0.8rem;
  font-weight: 500;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  margin-top: 0px;
}
.section {
  padding: 1rem !important;
}
.chain {
  position: absolute;
  top: 5px;
  right: 20px;
  font-size: 1.2rem;
}

body, button, input, optgroup, select, textarea {
  font-family: 'Open Sans', sans-serif !important;
}

.control .unstyled {
  margin-left: 0.5em;
}

button.unstyled {
  margin-right: 0.5em;
  height: 2.5em;
  line-height: 1.5;
  font-size: 0.75rem
}

.logo {
  font-size: 5rem;
}

.column {
  float: left;
  width: 40%;
}

.column-sm {
  float: left;
  width: 25%;
  margin-right: 1%;
}

.warning {
  font-size: 0.8rem;
}

.row:after {
  content: "";
  display: table;
  clear: both;
}

.apeTax {
  position: absolute;
  width: 100%;
  height: 100%;
  background-size: cover;
  background-color: purple;
}
</style>

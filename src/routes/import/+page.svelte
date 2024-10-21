<script lang="ts">
import WebApp from '@twa-dev/sdk'
import { onMount } from 'svelte'
import { hdKeyToAccount, hex, HDKey } from '@mina-js/accounts'
import { mnemonicToSeedSync } from '@scure/bip39'
import Cryptr from 'cryptr'

const importWallet = async () => {
  const cryptr = new Cryptr(pin)
  const seed = mnemonicToSeedSync(mnemonic)
  const hdKey = HDKey.fromMasterSeed(seed)
  const account = hdKeyToAccount({ hdKey, addressIndex: 0 })
  const privKey = hex.encode(hdKey.privKeyBytes)
  const encryptedChild = cryptr.encrypt(privKey)
  console.log('>>>EC1', encryptedChild)
  await WebApp.CloudStorage.setItem('pallad:key', encryptedChild)
  const palladKey = await WebApp.CloudStorage.getItem('pallad:key')
  const retrived = cryptr.decrypt(palladKey)
  console.log('>>>EC2', retrived)
}

let mnemonic = $state('')
let pin = $state('')
</script>

<form on:submit|preventDefault={importWallet} class="flex flex-1 flex-col justify-between">
  <div class="flex flex-col gap-4">
  <div class="flex flex-col gap-2">
    <label>Recovery Phrase</label>
    <textarea class="textarea textarea-bordered" bind:value={mnemonic} />
  </div>
  <div class="flex flex-col gap-2">
    <label>Pin Code</label>
    <input type="password" class="input input-bordered" bind:value={pin} />
  </div>
  </div>
  <button type="submit" class="btn btn-primary">Import</button>
</form>


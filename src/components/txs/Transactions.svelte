<script lang="ts">
  import { _ } from "svelte-i18n";
  import { onDestroy, onMount } from "svelte";
  import { signingAccount } from "../../accounts";
  import { printCoins } from "../../coinHelpers";
  import { checkSigningAccountBalance } from "../../accountActions";
  import TransferDialog from "./TransferDialog.svelte";

  let account;
  let unsubs;

  onMount(async () => {
    unsubs = signingAccount.subscribe(obj => account = obj)
  });

  onDestroy(async () => {
    unsubs && unsubs();
  });

  const onSuccess = () => {
    checkSigningAccountBalance();
  }

</script>

<main>
  <div class="uk-flex uk-flex-center">
    <h2 class="uk-text-light uk-text-muted uk-text-uppercase">{$_("nav.transactions")}</h2>
  </div>

  <!-- Account info -->
  {#if account}
    <div class="uk-text-center">
      <div class="uk-section">
        <p><span class="uk-text-uppercase">{$_("txs.account")}: </span><span class="uk-text-bold">{account.account}</span></p>
        <p><span class="uk-text-uppercase">{$_("txs.balance")}: </span><span class="uk-text-bold">{printCoins(account.balance)}</span></p>
      </div>
    
      <button uk-toggle="target: #onboardDialog" class="uk-button uk-button-default uk-margin-right">{$_("txs.btn_onboard")}</button>
      <button uk-toggle="target: #coinTransferDialog" class="uk-button uk-button-primary">{$_("txs.btn_transfer")}</button>
    </div>
    <TransferDialog {account} {onSuccess}/>
  {:else}
    Loading...
  {/if}
</main>
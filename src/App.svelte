<script lang="ts">
  import { onDestroy, onMount } from "svelte";
  import { Router, Route } from "svelte-navigator";
  import Nav from "./components/Nav.svelte";
  import DebugCard from "./components/dev/DebugCard.svelte";
  import Wallet from "./components/wallet/Wallet.svelte";
  import Settings from "./components/settings/Settings.svelte";
  import DevMode from "./components/dev/DevMode.svelte";
  import AccountFromMnemForm from "./components/wallet/AccountFromMnemForm.svelte";
  import Keygen from "./components/wallet/Keygen.svelte";
  import Transactions from "./components/txs/Transactions.svelte";
  import Events from "./components/events/Events.svelte";
  import About from "./components/about/About.svelte";

  import { getEnv, debugMode } from "./debug";
  import { routes } from "./routes";
  import "uikit/dist/css/uikit.min.css";
  import { isWalletInit} from "./accountActions";
  import { getVersion } from "./version";
  import { walletTick } from "./tick";
  import { init_preferences } from "./preferences";
  
  init_preferences();

  let healthTick;
  let debug = false;

  onMount(async () => {

    isWalletInit();

    getEnv();

    getVersion();

    walletTick();
    healthTick = setInterval(walletTick, 30000); // do a healthcheck, this is async

    debugMode.subscribe(b => debug = b);
    
  });

  onDestroy(() => {
    clearInterval(healthTick);
  })
</script>

<main class="uk-background-muted uk-height-viewport">
  <div class="uk-container">
    <Router>
      <Nav />
      <div class="uk-background-muted uk-margin-large">
        <Route path={routes.home} component={Wallet} primary={false} />
        <Route
          path={routes.accountFromMnem}
          component={AccountFromMnemForm}
          primary={false}
        />
        <Route path={routes.keygen} component={Keygen} primary={false} />
        <Route path={routes.transactions} component={Transactions} primary={false} />
        <Route path={routes.events} component={Events} primary={false} />
        <Route path={routes.settings} component={Settings} primary={false} />
        <Route path={routes.about} component={About} primary={false} />

        <!-- DEV -->
        <Route path={routes.developer} component={DevMode} primary={false} />

        <!-- Show Debug Card Below -->
        {#if debug }
          <DebugCard/>
        {/if}
      </div>
    </Router>
  </div>  
</main>

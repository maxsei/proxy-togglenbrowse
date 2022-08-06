<script lang="ts">
  import { onMount } from "svelte";
  import browser from "webextension-polyfill";


  let proxyType = "none";
  function getProxySettings(){
    let proxySettings;
    browser.proxy.settings.get({}).then((x) => {
      proxySettings = x;
    });
    return proxySettings;
  }
  onMount(async () => {
    let proxySettings = getProxySettings();
    proxyType = proxySettings.proxyType || "none";
  });
  function handleClick(e){
	console.log(e)
  	if (proxyType === "none") {
		proxyType = "manual";
		return;
	}
	proxyType = "none";
  };
</script>

<div id="app">
	<button id="toggle" class="proxytype-{proxyType}" on:click={handleClick}>
		hello
	</button>
</div>

<style>
  #app {
    display: grid;
    grid-template-rows: 20px auto;
    bottom: 0;
    left: 0;
    position: absolute;
    right: 0;
    top: 0;
    font-size: 1rem;
    margin: 0;
    height: 400px;
    width: 400px;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
      Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
  }
  #toggle {
  	
  }
  .proxytype-none {
	  background-color: none,
  }
  .proxytype-manual {
	  background-color: purple,
  }
</style>

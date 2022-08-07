<script lang="ts">
	import { onMount } from "svelte";
	import browser from "webextension-polyfill";


	let proxyType = "none";
  function setNested(obj, keys: Array<string>, value) {
    const key = keys[0];
    if (keys.length == 1)
      return {...obj, [key]: value};
    const nested = setNested(obj[key], keys.slice(1), value);
    return {...obj, [key]: nested};
  };

	onMount(async () => {
    browser.proxy.settings.get({}).then((proxySettings)=>{
      proxyType = proxySettings.value.proxyType || "none";
    }).catch((err)=>{
      alert(`getting proxy settings on mount: ${err}`);
    });
	});

	function handleClick(e){
    browser.proxy.settings.get({}).then((proxySettings)=>{
      const proxyTypeNew = (proxySettings.value.proxyType === "none") ? "manual" : "none";
      const proxySettingsNew = setNested(proxySettings, ["value", "proxyType"], proxyTypeNew);
      browser.proxy.settings.set({value: proxySettingsNew.value}).then(()=>{
        proxyType = proxyTypeNew;
      }).catch((err)=>{
        alert(`setting proxy settings: ${err}`);
      });
    }).catch((err)=>{
      alert(`getting proxy settings: ${err}`);
    });
	};
</script>

<div id="app">
	<div id="toggle" class="proxytype-{proxyType}" on:click={handleClick}>
    Toggle Manual Network Configuration: {proxyType === "manual" ? "ON" : "OFF"}
	</div>
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
	.proxytype-none {
		background-color: red;
	}
	.proxytype-manual {
		background-color: purple;
	}
</style>

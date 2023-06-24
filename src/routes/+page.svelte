<script lang="ts">
    import { whatsabi } from "@shazow/whatsabi";
    import { ethers } from "ethers";

    import NFTWidget from '../components/NFTWidget.svelte';

    const provider = new ethers.providers.InfuraProvider("homestead", "22af2ac3832349cfb8b5c9e6d3b6197b");

    let loading = false;
    let address = "";
    let abi = [];
    let typedWidget = false;
    let widgetArgs = {};
    let tokenId = "94"; // TODO: Unhardcode this

    function detectWidget(abi) {
        // TODO: The rest of the owl lol
        typedWidget = NFTWidget;
        widgetArgs = {
            address,
            tokenId,
        }
    }

    function loadABI(a) {
        if (loading) return;
        loading = true;
        address = a;
        console.log("Loading address:", address);
        whatsabi.autoload(address, {
            provider,
            abiLoader: new whatsabi.loaders.EtherscanABILoader(),
            //signatureLookup: new whatsabi.loaders.OpenChainSignatureLookup(),
        }).then((result) => {
            abi = result;
            detectWidget(abi);
        }).finally(() => {
            loading = false;
        });
    }

    function onSubmit(e) {
        const f = new FormData(e.target);
        const a = f.get("address") || "";
        if (!a) return;

        loadABI(a);
    }

    function searchAddress(e) {
        loadABI(e.target.innerText);
    }
</script>


<header>
    <h1>Contract Explorer</h1>
    <img class="logo-img" src="logo-large.png">
    <p>Like Internet Explorer, but for contracts.</p>
</header>

<form on:submit|preventDefault={onSubmit} disabled="{!loading}">
    <div class="search">
        <input type="text" class="searchTerm contract-search" name="address" value={address} placeholder="> contract address">
        <button type="submit" class="button searchButton">Search</button>
    </div>
</form>
{#if loading}

<div class="throbber" >
    Loading...
</div>
{/if}


<div class="center examples-text">
    <h2>Examples</h2>
    <p>Nouns: <a on:click={searchAddress}>0x9C8fF314C9Bc7F6e59A9d9225Fb22946427eDC03</a></p>
    <p>MakerDAO:</p>
    <p>ApeCoin:</p>
    <p>Random unverified contract:</p>
</div>

<svelte:component this={typedWidget} {...widgetArgs} />

<div class="content-container abi-dump">
<code>
    <button onclick="myFunction()" class="button">Copy text</button>
    {JSON.stringify(abi)}

    <!-- <table>
        <thead>
            <tr>
                <th>Type</th>
                <th>Payable</th>
                <th>State Mutability</th>
            </tr>
        </thead>
        <tbody>
            {#each abi as selector, i}
                <tr>
                    <td class="border border-slate-300">{abi[i].type}</td>
                    <td class="border border-slate-300">{abi[i].payable}</td>
                    <td class="border border-slate-300">{abi[i].stateMutability}</td>
                </tr>
            {/each}
        </tbody>
    </table> -->
</code>
</div>

<style src="./base.scss" lang="scss" />

<script lang="ts">
    import { whatsabi } from "@shazow/whatsabi";
    import { ethers } from "ethers";

    import NFTWidget from '../components/NFTWidget.svelte';
    import TokenWidget from '../components/NFTWidget.svelte';

    const provider = new ethers.providers.InfuraProvider("homestead", "22af2ac3832349cfb8b5c9e6d3b6197b");

    let loading = false;
    let address = "";
    let abi = [];
    let typedWidget = false;
    let widgetArgs = {};
    let tokenId = "94"; // TODO: Unhardcode this

    function detectWidget(abi) {
        if (abi.filter((a) => a.type === "function" && a.name === "tokenURI").length > 0) {
            typedWidget = NFTWidget;
            widgetArgs = {
                address,
                tokenId,
            }
            return;
        }
        if (abi.filter((a) => a.type === "function" && a.name === "allowance").length > 0) {
            // TODO: ...
            //typedWidget = TokenWidget;
            //widgetArgs = {
            //    address,
            //    tokenId,
            //}
            return;
        }
        console.log("unknown type, no specialized widget yet");
    }

    async function loadABI(a) {
        if (loading) return;
        loading = true;
        address = a;
        console.log("Loading address:", address);
        const codeLoader = new whatsabi.loaders.EtherscanABILoader({ apiKey: "SHT8M9JSGR62U5U7YVFUSTPG41IVR1F7ND" });
        abi = await codeLoader.loadABI(address);
        if (abi.length > 0) {
            detectWidget(abi);
            loading = false;
            return;
        }

        const code = await provider.getCode(address);
        const selectors = whatsabi.selectorsFromBytecode(code);
        const signatureLookup = new whatsabi.loaders.OpenChainSignatureLookup();
        selectors.map((selector) => {
            const signature = signatureLookup.loadFunctions(selector);
            if (signature.length > 0) {
                abi.push(signature[0]);
            }
        });

        loading = false;
        detectWidget(abi);
        return;
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
    <img class="logo-img" src="logo-large.png">
    <h1>Contract Explorer</h1>
    <p>Like Internet Explorer, but for contracts.</p>
</header>

<form on:submit|preventDefault={onSubmit} disabled="{!loading}">
        <input type="text" class="searchTerm contract-search" name="address" value={address} placeholder="> contract address">
        <button type="submit" class="button searchButton">Search</button>
</form>

<section class="container">

{#if loading}
<div class="throbber" >
    <img class="spinner" src="spinner.png" alt="">
</div>
{/if}

<div class="examples-text">
    <h2>Examples</h2>
    <ul>
        <li>Nouns: <a on:click={searchAddress}>0x9C8fF314C9Bc7F6e59A9d9225Fb22946427eDC03</a></li>
        <li>ApeCoin: <a on:click={searchAddress}>0x4d224452801aced8b2f0aebe155379bb5d594381</a></li>
        <li>Rando Unverified Contract: <a on:click={searchAddress}>0xab4f32686cf8687d881462fe29170fb17180155b</a></li>
    </ul>
</div>

<svelte:component this={typedWidget} {...widgetArgs} />

{#if abi.length > 0}
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
{/if}

</section>

<style src="./base.scss" lang="scss" />

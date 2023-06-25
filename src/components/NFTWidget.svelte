<script lang="ts">
    import { onMount } from 'svelte';

    export let address : string;
    export let tokenId : string;
    let blockchain = "ethereum";

    let nft = {
        id: null,
        metaData: {
            name: null,
            description: null,
            image: null,
            attributes: [],
        },
        contentValue: {
            image: {
                medium: null,
            }
        },
    };
    let ready = false;

    const AIRSTACK_API = "90d779ac375b4b45844023feed648173";
    const AIRSTACK_ENDPOINT = "https://api.airstack.xyz/gql";
    
    import { request, GraphQLClient, gql } from 'graphql-request'
    const client = new GraphQLClient(AIRSTACK_ENDPOINT, {
        headers: {
            authorization: AIRSTACK_API,
        },
    });
    const query = gql`
      query GetLatestNFTPreview($address: Address!, $tokenId: String!, $blockchain: TokenBlockchain!) {
        TokenNft(input: {address: $address, tokenId: $tokenId, blockchain: $blockchain}) {
          id
          metaData {
            name
            description
            image
            attributes {
              trait_type
              value
            }
          }
          contentValue {
            image {
              medium
            }
          }
        }
      }`;


    async function loadNFT(address, tokenId, blockchain) {
        if (!address || !tokenId) {
            throw "loadNFT: Missing arguments";
        }
        console.log("Loading NFT:", address, tokenId);
        const r = await client.request(query, {
            address, tokenId, blockchain
        });
        return r;
    }

    function onSubmit(e) {
        const f = new FormData(e.target);
        const id = f.get("tokenId") || "";
        if (!id) return;

        tokenId = id;
        load();
    }

    async function load() {
        ready = false;
        const r = await loadNFT(address, tokenId, blockchain);
        nft = r.TokenNft;
        ready = true;
    }

    onMount(load);
</script>


{#if ready}
<div class="center content-container contract-hero">
  <img class="nft-img" src="{nft.contentValue.image.medium}">
  <form on:submit|preventDefault={onSubmit}>
      <div class="search">
          <input type="text" class="searchTerm nft-search" name="tokenId" value={tokenId} placeholder="> NFT TOKEN ID">
          <button type="submit" class="button searchButton">Search</button>
      </div>
      <p>{nft.metaData.name} - {nft.metaData.description}</p>
  </form>
</div>
{:else}
<div>Loading...</div>
{/if}


<style src="./NFTWidget.scss" lang="scss" />

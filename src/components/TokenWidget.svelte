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
        }
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
        }
      }`;


    async function loadToken(address, tokenId, blockchain) {
        if (!address || !tokenId) {
            throw "loadNFT: Missing arguments";
        }
        const r = await client.request(query, {
            address, tokenId, blockchain
        });
        return r;
    }

    onMount(async () => {
        const r = await loadToken(address, tokenId, blockchain);
        nft = r.Token;
        ready = true;
    });
</script>


{#if ready}
<div>
    {nft.metaData.name} - {nft.metaData.description}
    <img src="{nft.metaData.image}" />
</div>
{:else}
<div>Loading...</div>
{/if}

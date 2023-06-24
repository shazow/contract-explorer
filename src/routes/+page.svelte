<script lang="ts">
  function onSubmit(e) {
    const formData = new FormData(e.target);

    const data = {};
    for (let field of formData) {
      const [key, value] = field;
      data[key] = value;
    }
    console.log(data)
  }
</script>

<header>
    <h1>Contract Explorer</h1>
</header>

<p>Like Internet Explorer, but for contracts.</p>

<form class="center" on:submit|preventDefault={onSubmit}>
    <div class="search">
        <input type="text" class="searchTerm" placeholder="> contract address">
        <button type="submit" class="searchButton">Search</button>
    </div>
</form>

<div>
    <p>Examples:</p>
    <p>Nouns: 0x9C8fF314C9Bc7F6e59A9d9225Fb22946427eDC03</p>
    <p>MakerDAO:</p>
    <p>ApeCoin:</p>
    <p>Random unverified contract:</p>
</div>

<div class="content-container">
    <p>Examples:</p>
    <p>Nouns: 0x9C8fF314C9Bc7F6e59A9d9225Fb22946427eDC03</p>
    <p>MakerDAO:</p>
    <p>ApeCoin:</p>
    <p>Random unverified contract: xx</p>
</div>


<style>
    :global(body) {
	    --tw-gradient-to: #3b82f6;
	    --tw-gradient-from: #06b6d4;
    	--tw-gradient-stops: var(--tw-gradient-from), var(--tw-gradient-to);
    	background-image: linear-gradient(to right, var(--tw-gradient-stops));
	}

    .center{
        margin: auto;
        width: 50%;
    }

    .search{
        margin-top: 100px;
        margin-left: 100px;
    }

    .searchTerm {
		background: #00000057;
		padding: 15px;
		border-radius: 5px;
		color: white;
		min-width: 500px;
		border-width: 0.7em;
		border-color: #ffffff0d;
	}

	.searchButton {
		width: 100px;
		height: 36px;
		border: none;
		background: #00000028;
		text-align: center;
		color: #ffffff9a;
		margin-left: -125px;
	}

	::placeholder { /* Chrome, Firefox, Opera, Safari 10.1+ */
		color: rgba(255, 255, 255, 0.542);
	}

	input:focus, select:focus, textarea:focus, button:focus {
    	outline: none;
	}

	.content-container{
		background-color: rgba(0, 0, 0, 0.122);
	}
</style>

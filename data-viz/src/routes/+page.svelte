<script lang="ts">
	import Dbcontrol from '$lib/components/dbcontrol.svelte';
	import Chatbot from '$lib/components/chatbot.svelte';
	import Header from '$lib/components/header.svelte';
	import DataViz from '$lib/components/dataViz.svelte';

	let chatting = true;
	let dbOpen = false;

	function submit_chat() {
		// Do the call to the submit api call
	}
</script>

<div class="Page">
	<Header />
	<div class="columnsDiv" style="">
		{#if chatting}
			{#if dbOpen}
				<div class="centerDiv">
					<Dbcontrol />
					<div class="buttonDiv">
						<button
							class="ChangeStateButton"
							on:click={() => {
								dbOpen = !dbOpen;
							}}>Close database tab</button
						>
					</div>
				</div>
			{:else}
				<div class="centerDiv">
					<Chatbot />
					<div class="buttonDiv">
						<button
							class="ChangeStateButton"
							on:click={() => {
								chatting = !chatting;
								submit_chat();
							}}>See where you Stand</button
						>
						<button
							class="ChangeStateButton"
							on:click={() => {
								dbOpen = !dbOpen;
							}}
						>
							Open database tab</button
						>
					</div>
				</div>
			{/if}
		{:else}
			<div class="centerDiv">
				<DataViz />
				<div class="buttonDiv">
					<button
						class="ChangeStateButton"
						on:click={() => {
							chatting = !chatting;
						}}>Want to chat again?</button
					>
				</div>
			</div>
		{/if}
	</div>
</div>

<style>
	.Page {
		position: absolute;
		display: flex;
		height: calc(100%);
		width: calc(100%);
		min-height: 100vh;
		flex-direction: column;
		overflow: auto;
		border: 5px solid #340a5e;
		border-radius: 2px;
		z-index: 99999;
		background: radial-gradient(#2d0042 0%, #040024 100%);
	}

	.columnsDiv {
		position: relative;
	}

	.ChangeStateButton {
		padding: 0.5rem 1rem;
		/* left: calc(40%); */
		width: calc(50%);
		border: none;
		background-color: #0077cc;
		color: white;
		border-radius: 0.5rem;
		cursor: pointer;
	}

	.centerDiv {
		margin: 2rem auto;
	}

	.buttonDiv {
		display: flex;
		flex-direction: row;
		max-width: calc(70% - 5px);
		align-items: stretch;
		column-gap: 1rem;
		margin: 2rem auto;
		background: #372f2f;

		border-radius: 1rem;
		padding: 1rem;
		box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
	}

	.ChangeStateButton:hover {
		background-color: #005fa3;
	}
</style>

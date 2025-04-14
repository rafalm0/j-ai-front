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
				<div>
					<Dbcontrol />
					<button
						class="ChangeStateButton"
						on:click={() => {
							dbOpen = !dbOpen;
						}}>Close database tab</button
					>
				</div>
			{:else}
				<div>
					<Chatbot />
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
			{/if}
		{:else}
			<div>
				<DataViz />
				<button
					class="ChangeStateButton"
					on:click={() => {
						chatting = !chatting;
					}}>Want to chat again?</button
				>
			</div>
		{/if}
	</div>
</div>

<style>
	.Page {
		left: 2px;
		position: absolute;
		width: calc(100% - 5px);
		height: calc(100% - 5px);
		border: 5px solid #541990;
		border-radius: 10px;
		/* z-index: 99999; */
		background: #a239ca !important;
	}

	.columnsDiv {
	}

	.ChangeStateButton {
		padding: 0.5rem 1rem;
		left: calc(40%);
		border: none;
		background-color: #0077cc;
		color: white;
		border-radius: 0.5rem;
		cursor: pointer;
	}

	.ChangeStateButton:hover {
		background-color: #005fa3;
	}
</style>

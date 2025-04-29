<script lang="ts">
	let messages: { bot: string; text: string }[] = [];
	let input = '';
	let session_id = '';

	async function startConversation() {
		if (!input.trim()) return;

		messages = [];
		const body = JSON.stringify({
			topic: input,
			session_id: session_id || null,
			continue_conversation: false
		});
		input = '';

		const options = {
			method: 'POST',
			headers: { 'Content-Type': 'application/json' },
			body
		};

		const response = await fetch('http://127.0.0.1:8000/multi-agent-chat', options);
		const data = await response.json();

		session_id = data.session_id;
		messages = [...messages, { bot: data.bot_name, text: data.response }];
	}

	async function continueConversation() {
		if (!session_id) return;

		const body = JSON.stringify({
			topic: '', // No need to resend topic
			session_id,
			continue_conversation: true
		});

		const options = {
			method: 'POST',
			headers: { 'Content-Type': 'application/json' },
			body
		};

		const response = await fetch('http://127.0.0.1:8000/multi-agent-chat', options);
		const data = await response.json();

		messages = [...messages, { bot: data.bot_name, text: data.response }];
	}
</script>

<div class="chat-container">
	<div class="messages">
		{#each messages as msg}
			<div class="message">
				<strong>{msg.bot}:</strong>
				{msg.text}
			</div>
		{/each}
	</div>

	<div class="input-row">
		<input
			bind:value={input}
			on:keydown={(e) => e.key === 'Enter' && startConversation()}
			placeholder="Enter a topic to start..."
		/>
		<button on:click={startConversation}>Start New Topic</button>
		<button on:click={continueConversation}>Continue Talking</button>
	</div>
</div>

<style>
	.chat-container {
		max-width: calc(70% - 5px);
		min-width: calc(350px);
		height: fit-content;
		margin: 2rem auto;
		background: #372f2f;
		border-radius: 1rem;
		padding: 1rem;
		box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
	}

	.messages {
		overflow-y: auto;
		margin-bottom: 1rem;
	}

	.message {
		padding: 0.5rem;
		margin: 0.5rem 0;
		border-radius: 0.5rem;
		background-color: #fff;
	}

	.input-row {
		display: flex;
		gap: 0.5rem;
		flex-wrap: wrap;
	}

	input {
		flex: 1;
		padding: 0.5rem;
		border-radius: 0.5rem;
		border: 1px solid #ccc;
	}

	button {
		padding: 0.5rem 1rem;
		border: none;
		background-color: #0077cc;
		color: white;
		border-radius: 0.5rem;
		cursor: pointer;
	}

	button:hover {
		background-color: #005fa3;
	}
</style>

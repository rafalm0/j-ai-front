<script lang="ts">
	let messages: { role: 'user' | 'bot'; text: string }[] = [];
	let input = '';
	let session_id = '';

	function sendMessage() {
		if (!input.trim()) return;
		messages = [...messages, { role: 'user', text: input }];

		let body = `{"message" : "${input}"}`;
		if (session_id != '') {
			body = `{"message" : "${input}", "session_id": "${session_id}"}`;
		}
		input = '';
		const options = {
			method: 'POST',
			headers: { 'Content-Type': 'application/json', 'User-Agent': 'insomnia/10.3.1' },
			body: body
		};

		fetch('http://127.0.0.1:8000/chat', options)
			.then((response) => response.json())
			.then((response) => {
				messages = [...messages, { role: 'bot', text: response.response }];
				console.log(response.data_collected);
				session_id = response.session_id;
			})
			.catch((err) => console.error(err));
	}
</script>

<div class="chat-container">
	<div class="messages">
		{#each messages as msg}
			<div class={`message ${msg.role}`}>
				<strong>{msg.role === 'user' ? 'You' : 'Bot'}:</strong>
				{msg.text}
			</div>
		{/each}
	</div>

	<div class="input-row">
		<input
			bind:value={input}
			on:keydown={(e) => e.key === 'Enter' && sendMessage()}
			placeholder="Write your message..."
		/>
		<button on:click={sendMessage}>Send</button>
	</div>
</div>

<style>
	.chat-container {
		max-width: calc(70% - 5px);
		min-width: calc(350px);
		margin: 2rem auto;
		background: #372f2f;
		border-radius: 1rem;
		padding: 1rem;
		box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
	}

	.messages {
		max-height: 300px;
		overflow-y: auto;
		margin-bottom: 1rem;
	}

	.message {
		padding: 0.5rem;
		margin: 0.5rem 0;
		border-radius: 0.5rem;
	}

	.message.user {
		background-color: #81e9c6;
		text-align: right;
	}

	.message.bot {
		background-color: rgb(255, 255, 255);
		text-align: left;
	}

	.input-row {
		display: flex;
		gap: 0.5rem;
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

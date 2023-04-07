<style lang="scss">
	:global(body) {
		background: #090912;
	}
	@keyframes shake {
		0% {
			transform: translate(-50%, -50%);
		}
		10% {
			transform: translate(-55%, -40%);
		}
		20% {
			transform: translate(-50%, -60%);
		}
		30% {
			transform: translate(-55%, -40%);
		}
		40% {
			transform: translate(-50%, -60%);
		}
		50% {
			transform: translate(-55%, -40%);
		}
	}
	.shake {
		animation: shake 0.3s;
	}
	div.ball {
		background: #090914;
		overflow: hidden;
		height: 40rem;
		width: 40rem;
		position: relative;
		left: 50%;
		top: 50%;
		transform: translate(-50%, -50%);
		border-radius: 50%;
		position: absolute;
		box-shadow: inset -1.5rem -1.5rem 1rem #080812, inset 2rem 2rem 5rem rgba(100, 100, 120, 0.1);
		cursor: pointer;
	}
	div.glare {
		background: rgba(130, 130, 120, 0.1);
		margin: auto;
		position: absolute;
		top: 4rem;
		left: -1rem;
		height: 20rem;
		width: 25rem;
		filter: blur(2rem);
		border-radius: 50%;
	}
	.screen {
		background: rgba(10, 10, 25);
		width: 30rem;
		height: 10rem;
		position: absolute;
		left: 5rem;
		top: 15rem;
		border-radius: 5px;
		box-shadow: -0.1rem -0.1rem 0.5rem -0.4rem rgb(10, 10, 20), 0.1rem 0.1rem 0.4rem -0.4rem yellow;
		overflow: hidden;
	}
	.fluid {
		position: absolute;
		left: 0;
		top: 0;
		right: 0;
		bottom: 0;
		background: rgba(10, 10, 28);
		transition: opacity 1s;
		display: flex;
		align-items: center;
		justify-content: center;
		opacity: 0;

		.answered & {
			opacity: 0.2;
		}
	}
	.answer {
		color: black;
		--answer-background: #cc9;
		box-shadow: 0 0 2.5rem 5rem var(--answer-background);
		font-family: sans-serif;
		white-space: nowrap;
		width: 1px;
		height: 1px;
		display: flex;
		align-items: center;
		justify-content: center;
		border-radius: 50%;
		transition: transform 3s;
		transform: rotate(var(--answer-rotation)) scale(0.7);

		.answered & {
			transform: rotate(var(--answer-rotation)) scale(1);
		}
	}
</style>

<main>
	<div
		class="ball"
		class:answered
		class:shake
		style:--answer-rotation="{rotation}deg"
		on:mouseup={makeAnswer}
		on:keydown={keydown}
	>
		<div class="glare" />
		<div class="screen">
			<div class="fluid">
				<div class="answer">{answer}</div>
			</div>
		</div>
	</div>
</main>

<script lang="ts">
	import { onDestroy, onMount } from 'svelte';

	const answers = ['Yeah', 'So true', 'No way yo', 'Wrong!', 'sus', '...', ';)', 'Not worth your time'],
		autoMode = false;

	let timer: ReturnType<typeof setInterval>,
		answered = false,
		working = false,
		shake = false,
		rotation = 0,
		answer = '';
	makeAnswer();

	if (autoMode) {
		onMount(() => {
			timer = setInterval(() => {
				makeAnswer();
			}, 2000);
		});

		onDestroy(() => {
			clearInterval(timer);
		});
	}

	async function sleep(s: number) {
		return new Promise((resolve) => setTimeout(resolve, s * 1000));
	}

	async function makeAnswer() {
		if (working) {
			return;
		}

		answered = !answered;
		if (answered) {
			answer = answers[Math.floor(Math.random() * answers.length)];
			working = true;
			shake = true;
			await sleep(0.5);
			shake = false;
			rotation = Math.floor(Math.random() * 60) - 30;
			working = false;
		}
	}

	function keydown(e: KeyboardEvent) {
		if (['Space', 'Enter'].includes(e.key)) {
			makeAnswer();
		}
	}
</script>

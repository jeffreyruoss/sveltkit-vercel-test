<script>
	import { onMount } from 'svelte';
	import cardBack from '$lib/images/card-back.jpg';
	let images = [];
	let hasFlippedCard = false;
	let lockBoard = false;
	let firstCard, secondCard;

	onMount(async () => {
			const res = await fetch('https://data.rarepepes.com/items/nft');
			const data = await res.json();
			images = data.data.map(item => item.img_url).slice(0, 10);
			images = images.concat(images).sort(() => Math.random() - 0.5);
	});

	const flipCard = (e, image) => {
			if (lockBoard) return;
			if (e.target === firstCard) return;

			e.target.src = image;
			e.target.classList.add('flip');

			if (!hasFlippedCard) {
					hasFlippedCard = true;
					firstCard = e.target;
					return;
			}

			secondCard = e.target;
			checkForMatch();
	};

	const checkForMatch = () => {
			let isMatch = firstCard.src === secondCard.src;
			isMatch ? disableCards() : unflipCards();
	};

	const disableCards = () => {
			firstCard.removeEventListener('click', flipCard);
			secondCard.removeEventListener('click', flipCard);
			resetBoard();
	};

	const unflipCards = () => {
			lockBoard = true;
			setTimeout(() => {
					firstCard.src = '../src/lib/images/card-back.jpg';
					secondCard.src = '../src/lib/images/card-back.jpg';
					firstCard.classList.remove('flip');
					secondCard.classList.remove('flip');
					resetBoard();
			}, 1500);
	};

	const resetBoard = () => {
			[hasFlippedCard, lockBoard] = [false, false];
			[firstCard, secondCard] = [null, null];
	};
</script>

<div class="cards-container">
	{#each images as image, i (i)}
		<img src="{cardBack}" class="card" on:click={e => flipCard(e, image)} />
	{/each}
</div>

<style>
	.cards-container {
			display: flex;
			flex-wrap: wrap;
			justify-content: center;
	}
	.card {
			width: 175px;
			height: 175px;
			border: 1px solid black;
			display: inline-block;
			margin: 10px;
			background-image: url('../lib/images/card-back.jpg');
	}
	.card.flip {
			background-image: none;
	}
</style>



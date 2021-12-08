<template>
	<div class="dialog-boxes">
		<div class="text">{{ messages[messageIndex] }}</div>
		<div class="options">
			<div @click="nextMessage">Yes, let's do this</div>
			<div>No way</div>
		</div>
	</div>
</template>

<script>
	import anime from 'animejs/lib/anime.es.js';

	export default {
		data() {
			return {
				messageIndex: 0,
				messages: [
					'Minim nostrud sit nisi excepteur reprehenderit duis proident?',
					'Velit eu?',
					'Irure et in laboris, iadatur?',
				]
			}
		},
		methods: {
			nextMessage() {
				this.messageIndex++;
				if (this.messageIndex >= this.messages.length) {
					this.messageIndex = 0;
				}
			}
		},
		mounted() {
			var items = this.$el.querySelector('.text, .options>div');
			anime({
			  targets: items,
			  opacity: [0, 1],
			  translateY: ['20px', 0],
			  delay: anime.stagger(500) // increase delay by 100ms for each elements.
			});
		}
	}
</script>
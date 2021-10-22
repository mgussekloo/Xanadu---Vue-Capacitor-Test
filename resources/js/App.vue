<template>
	<img src="static/tarot.jpg" ref="image" />
</template>

<script>
	import anime from 'animejs/lib/anime.es.js';

	export default {
		name: 'App',
		mounted() {
			anime({
				targets: this.$refs.image,
				opacity: [0, 1],
				translateY: [10, 0],
				easing: 'easeInOutQuad'
			});
		}
	}
</script>
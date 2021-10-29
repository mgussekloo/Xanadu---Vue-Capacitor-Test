<template>
	<div></div>
</template>

<script>
	import anime from 'animejs/lib/anime.es.js';
	import * as PIXI from 'pixi.js'

	export default {
		name: 'App',
		mounted() {
			const app = new PIXI.Application();
			app.renderer.resize(window.innerWidth, window.innerHeight);
			this.$el.appendChild(app.view);

			app.loader.add('tarot', 'static/tarot.jpg').load((loader, resources) => {
			    const img = new PIXI.Sprite(resources.tarot.texture);

			    // app.renderer.view.style.width = window.innerWidth;
			    // app.renderer.view.style.height = window.innerHeight;

			    img.x = app.renderer.width / 2;
			    img.y = app.renderer.height / 2;

			    img.anchor.x = 0.5;
			    img.anchor.y = 0.5;

			    app.stage.addChild(img);

			    app.ticker.add(() => {
			        img.rotation += 0.01;
			    });
			});

		}
	}
</script>
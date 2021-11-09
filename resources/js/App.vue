<template>
	<div @click="nextSlide()"></div>
</template>

<script>
	import anime from 'animejs/lib/anime.es.js';
	import * as PIXI from 'pixi.js';

	import { PixelateFilter } from 'pixi-filters';

	export default {
		name: 'App',
		data() {
			return {
				app: null,
				img: null,
				spriteIndex: 0,
				spriteData: [
					{ name: 'nook', 	url: 'static/nook.webp' },
					{ name: 'blathers', url: 'static/blathers.webp' },
					{ name: 'brewster', url: 'static/brewster.webp' }
				],
				resources: null,
				pixelateFilter: null
			}
		},
		methods: {
			nextSlide() {
				var tweenObj = { size: 1 };

				anime({
					targets: tweenObj,
					duration: 1000,
					size: [1, 100],
					easing: 'linear',
					update: ()=>{
						this.pixelateFilter.size = tweenObj.size;
					},
				}).finished.then(() => {
					console.log(this.spriteIndex);
					return new Promise((resolve, reject) => {
						this.spriteIndex++;
						if (this.spriteIndex > 2) {
							this.spriteIndex = 0;
						}
						this.img.texture = this.resources[ this.spriteData[ this.spriteIndex ].name ].texture;

						resolve();
					});
				}).then(() => {
					anime({
						targets: tweenObj,
						duration: 1000,
						size: [100, 1],
						easing: 'linear',
						update: ()=>{
							this.pixelateFilter.size = tweenObj.size;
						},
					})
				});
			}
		},
		mounted() {
			var app = new PIXI.Application();
			app.renderer.resize(window.innerWidth, window.innerHeight);
			this.$el.appendChild(app.view);

			this.pixelateFilter = new PixelateFilter();
			this.pixelateFilter.size = 1;

			app.loader
				.add(this.spriteData)
				.load((loader, resources) => {

				    const img = new PIXI.Sprite(resources[ this.spriteData[ this.spriteIndex ].name ].texture);
				    img.filters = [ this.pixelateFilter ];

				    // app.renderer.view.style.width = window.innerWidth;
				    // app.renderer.view.style.height = window.innerHeight;

				    img.x = app.renderer.width / 2;
				    img.y = app.renderer.height / 2;

				    img.anchor.x = 0.5;
				    img.anchor.y = 0.5;

				    app.stage.addChild(img);

				    this.img = img;
				    this.resources = resources;

				    app.ticker.add(() => {
				        img.rotation += 0.01;
				    });
				});

			this.app = app;
		}
	}
</script>
<template>
	<div>
		<div ref="canvas" @click="nextSlide" @touchstart="nextSlide()"></div>
		<dialog-boxes></dialog-boxes>
	</div>
</template>

<script>
	import anime from 'animejs/lib/anime.es.js';
	import * as PIXI from 'pixi.js';
	import '@pixi/mixin-get-child-by-name';

	import { PixelateFilter } from 'pixi-filters';
	import * as particles from '@pixi/particle-emitter';

	import dialogBoxes from './dialogboxes.vue';

	let offset = require('mouse-event-offset');

	// var emitter = new PIXI.particles.Emitter(
	let c = new PIXI.Container();
	let pixelateFilter = new PixelateFilter();
	pixelateFilter.size = 1;
	c.filters = [ pixelateFilter ];

	let pc = new PIXI.ParticleContainer();

	let e = new particles.Emitter(pc, particles.upgradeConfig(
{
	"alpha": {
		"start": 1,
		"end": 0
	},
	"scale": {
		"start": 0.5,
		"end": 0.01,
		"minimumScaleMultiplier": 1
	},
	"color": {
		"start": "#e4f9ff",
		"end": "#3fcbff"
	},
	"speed": {
		"start": 200,
		"end": 50,
		"minimumSpeedMultiplier": 1
	},
	"acceleration": {
		"x": 0,
		"y": 0
	},
	"maxSpeed": 0,
	"startRotation": {
		"min": 0,
		"max": 360
	},
	"noRotation": false,
	"rotationSpeed": {
		"min": 0,
		"max": 0
	},
	"lifetime": {
		"min": 0.2,
		"max": 0.8
	},
	"blendMode": "normal",
	"frequency": 0.001,
	"emitterLifetime": 0.1,
	"maxParticles": 500,
	"pos": {
		"x": 0,
		"y": 0
	},
	"addAtBack": false,
	"spawnType": "circle",
	"spawnCircle": {
		"x": 0,
		"y": 0,
		"r": 0
	}
}, ['static/Sparks.png']
	));
	e.emit = false;

	export default {
		name: 'App',
		data() {
			return {
				app: null,
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

				// var pos = offset(event.changedTouches[0], this.$refs.canvas);
				// alert(pos);

				var tweenObj = { size: 1 };

				anime({
					targets: tweenObj,
					duration: 1000,
					size: [1, 100],
					easing: 'linear',
					update: ()=>{
						pixelateFilter.size = tweenObj.size;
					},
				}).finished.then(() => {
					console.log(this.spriteIndex);
					return new Promise((resolve, reject) => {
						this.spriteIndex++;
						if (this.spriteIndex > 2) {
							this.spriteIndex = 0;
						}
						c.getChildByName("Sprite").texture = this.resources[ this.spriteData[ this.spriteIndex ].name ].texture;

						resolve();
					});
				}).then(() => {
					anime({
						targets: tweenObj,
						duration: 1000,
						size: [100, 1],
						easing: 'linear',
						update: ()=>{
							pixelateFilter.size = tweenObj.size;
						},
					})

				});
			},
			clickCanvas(event) {
				var event = event.type.includes('touch') ? event.targetTouches[0] : event;
				var p = offset(event, this.$refs.canvas);

				e.resetPositionTracking();
				e.updateOwnerPos(p[0], p[1]);
				e.playOnce(() => {
				});
			}
		},
		mounted() {
 			this.$refs.canvas.addEventListener('mousedown', this.clickCanvas, false);
    		this.$refs.canvas.addEventListener('touchstart', this.clickCanvas, false);

			var app = new PIXI.Application();
			app.renderer.resize(window.innerWidth, window.innerHeight);
			this.$refs.canvas.appendChild(app.view);

			app.loader
				.add(this.spriteData)
				.load((loader, resources) => {
				    const img = new PIXI.Sprite(resources[ this.spriteData[ this.spriteIndex ].name ].texture);

				    // app.renderer.view.style.width = window.innerWidth;
				    // app.renderer.view.style.height = window.innerHeight;

				    img.name = "Sprite";
				    img.x = app.renderer.width / 2;
				    img.y = app.renderer.height / 2;

				    img.anchor.x = 0.5;
				    img.anchor.y = 0.5;

				    app.stage.addChild(c);
				    c.addChild(img);
				    app.stage.addChild(pc);

				    this.resources = resources;

				    app.ticker.add(() => {
				        img.rotation += 0.01;
				    });
				});

			app.ticker.add((delta) => {
				e.update( delta / PIXI.settings.TARGET_FPMS / 1000 );
			});

			this.app = app;
		},
		beforeUnmount() {
		    this.$refs.canvas.removeEventListener('mousedown', this.clickCanvas, false);
		    this.$refs.canvas.removeEventListener('touchstart', this.clickCanvas, false);
		},
		components: {
			dialogBoxes
		}

	}
</script>
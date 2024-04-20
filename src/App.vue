<template>
	<div id = "cesiumContainer" ></div>
</template>

<script lang="ts">
	import { defineComponent } from 'vue'
	import { Viewer, Ion, OpenStreetMapImageryProvider, ImageryLayer, CesiumTerrainProvider, Cartesian3 } from "cesium";

	import { Cesium3DTileset } from 'cesium';
	import { Transforms } from 'cesium';
	import { Matrix4 } from 'cesium';
	import { HeadingPitchRange } from 'cesium';
	import { HeadingPitchRoll } from 'cesium';
	import { Terrain } from 'cesium';
	
	import "cesium/Build/Cesium/Widgets/widgets.css";

	Ion.defaultAccessToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiIwZjE0YjBiYy1iNjZiLTQ5ZWQtOTBmOS0yODQxZjYyMGQ0ZGIiLCJpZCI6MjY5MDcsImlhdCI6MTcxMzQzOTMxMH0.O4RDeS46mL3ABENB4wHIbiFPdCj8ze1xYPUrDrRVs1Y';

	declare global {
		interface Window { CESIUM_BASE_URL: string; }
	}

	window.CESIUM_BASE_URL = 'static/cesium';
	

	export default defineComponent({
		name: 'App',	
		async mounted() {
			
			let baseLayer = new ImageryLayer(new OpenStreetMapImageryProvider({
				url: "https://tile.openstreetmap.org/"
			}), {})

			let viewer = new Viewer("cesiumContainer", {
				baseLayer: baseLayer,
				terrain: Terrain.fromWorldTerrain(),
			});
			

			viewer.camera.setView({
				destination: new Cartesian3(1263093.7190924052, 5298125.807220887, 3311788.2669270574),
				orientation: {
					heading: 0.6291314004139439,
					pitch: -0.11972375171111294,
					roll: 6.283092953238719
				}
			});

			

			try {
				const tileset = await Cesium3DTileset.fromUrl(
				"static/tiles/tileset.json"
			);
				viewer.scene.primitives.add(tileset);

				var longitude = -2.630805;
				var latitude = 51.453787;
					var height = 135;
					var cartesian = Cartesian3.fromDegrees(longitude, latitude, height);
					var transform = Transforms.headingPitchRollToFixedFrame(cartesian, new HeadingPitchRoll(0.65));
					// tileset._root.transform = Matrix4.IDENTITY;
					tileset.modelMatrix = transform;
					viewer.zoomTo(tileset);
				
			} catch (error) {
				console.error(`Error creating tileset: ${error}`);
			}

			
			
		}
	})
</script>

<style>
html,
body,
	#cesiumContainer, #app {
		width: 100%;
		height: 100%;
		margin: 0;
		padding: 0;
		overflow: hidden;
		font-family: sans-serif;
	}
</style>

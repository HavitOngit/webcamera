<script lang="ts">
	import Card from '$lib/components/ui/card/card.svelte';
	import { onMount } from 'svelte';
	let vid: HTMLVideoElement;
	let canvas: HTMLCanvasElement;
	let img: HTMLImageElement;
	let permissionDeny = false;
	let facingMode = 'user';

	let constraints = {
		audio: false,
		video: {
			facingMode: facingMode
		}
	};

	function cam() {
		navigator.mediaDevices
			.getUserMedia(constraints)
			.then(function success(stream) {
				vid.srcObject = stream;
				permissionDeny = false;
			})
			.catch((error) => {
				permissionDeny = true;
				console.log(error);
			});
	}
	onMount(() => {
		vid.setAttribute('playsinline', '');
		vid.setAttribute('autoplay', '');
		vid.setAttribute('muted', '');

		/* Setting up the constraint */
		// Can be 'user' or 'environment' to access back or front camera (NEAT!)

		/* Stream it to video element */

		cam();
	});

	function capture() {
		canvas.width = vid.videoWidth;
		canvas.height = vid.videoHeight;

		canvas.getContext('2d').drawImage(vid, 0, 0);
		img.src = canvas.toDataURL('image/webp');

		//
		const aTag = document.createElement('a');
		document.body.appendChild(aTag);
		aTag.href = canvas.toDataURL('image/webp');
		aTag.download = 'Output.webp';
		aTag.click();
		document.body.removeChild(aTag);

		console.log('Taken');
	}
</script>

<div>
	<Card>
		<video bind:this={vid} />
	</Card>
</div>
<div class="flex justify-center">
	<button on:click={capture} class="p-6 z-10 fixed bottom-4">Capture</button>
</div>

<img src="//:0" alt="0" bind:this={img} />
<!-- svelte-ignore a11y-media-has-caption -->
<canvas bind:this={canvas} hidden />

<!-- svelte-ignore a11y-media-has-caption -->

<!-- <button on:click={cam}>Allow</button> -->

<style>
	div {
		height: 100vmax;
		width: auto;
	}
	video {
		height: max-content;
	}
</style>

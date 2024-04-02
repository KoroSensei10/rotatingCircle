<script lang="ts">
	import { onMount } from 'svelte';

	let fps = 60;
	$: interval = 1000 / fps;
	let lastTime = 0;

	// Get the canvas element
	let canvas: HTMLCanvasElement;
	let ctx: CanvasRenderingContext2D;

	// Set the center point coordinates
	let centerX: number = 0;
	let centerY: number = 0;

	let sinWavePath: { x: number; y: number }[] = [];
	let cosWavePath: { x: number; y: number }[] = [];

	// Set the radius and initial angle
	const radius = 100;
	let frequency = 1;
	let time = 0;

	function draw() {
		requestAnimationFrame(draw);

		const now = new Date().getTime();
		const elapsed = now - lastTime;

		if (elapsed > interval) {
			lastTime = now - (elapsed % interval);

			// Clear the canvas
			ctx.clearRect(0, 0, canvas.width, canvas.height);

			const { x, y } = drawPoint();
			drawSinLine(x, y);
			drawCosLine(x, y);

			drawWaves();

			// opti possible en échantillonant le temps 1 fois dans un tableau
			time += (2 * Math.PI * frequency) / fps;
			time = time % (2 * Math.PI);
		}
	}

	function drawSinLine(x: number, y: number) {
		ctx.beginPath();
		ctx.moveTo(x, y);
		ctx.lineTo(300, y);

		sinWavePath.unshift({ x: 300, y });

		if (sinWavePath.length > 1000) {
			sinWavePath.pop();
		}

		ctx.strokeStyle = 'green';
		ctx.stroke();
	}

	function drawCosLine(x: number, y: number) {
		ctx.beginPath();
		ctx.moveTo(x, y);
		ctx.lineTo(x, 300);

		cosWavePath.unshift({ x, y: 300 });

		if (cosWavePath.length > 1000) {
			cosWavePath.pop();
		}

		ctx.strokeStyle = 'purple';
		ctx.stroke();
	}

	function drawWaves() {
		ctx.beginPath();
		ctx.moveTo(sinWavePath[0].x, sinWavePath[0].y);
		sinWavePath.forEach((point, i) => {
			ctx.lineTo(point.x + i * 2 * Math.PI * (1 / fps) * 20, point.y);
		});

		ctx.strokeStyle = 'green';
		ctx.stroke();

		ctx.beginPath();
		ctx.moveTo(cosWavePath[0].x, cosWavePath[0].y);
		cosWavePath.forEach((point, i) => {
			ctx.lineTo(point.x, point.y + i * 2 * Math.PI * (1 / fps) * 20);
		});

		ctx.strokeStyle = 'purple';
		ctx.stroke();
	}

	// Function to draw the rotating point
	function drawPoint() {
		// Calculate the position of the point
		const x = centerX + radius * Math.cos(time);
		const y = centerY + radius * Math.sin(time);

		// center point
		ctx.beginPath();
		ctx.arc(centerX, centerY, 2, 0, Math.PI * 2);
		ctx.fillStyle = 'black';
		ctx.fill();

		// Draw the line from the center to the point
		ctx.beginPath();
		ctx.moveTo(centerX, centerY);
		ctx.lineTo(x, y);
		ctx.strokeStyle = 'blue';
		ctx.stroke();

		// Draw the point
		ctx.beginPath();
		ctx.arc(x, y, 5, 0, Math.PI * 2);
		ctx.fillStyle = 'red';
		ctx.fill();

		return { x, y };
	}

	onMount(() => {
		let tempCanvas = document.getElementById('myCanvas') as HTMLCanvasElement;
		let tempctx = tempCanvas.getContext('2d');
		if (!tempCanvas || !tempctx) {
			return;
		}
		canvas = tempCanvas;
		ctx = tempctx;
		centerX = canvas.width / 8;
		centerY = canvas.height / 6;
		draw();
	});
</script>

<canvas width="1000" height="800" id="myCanvas"></canvas>
<h2>Values :</h2>
<label for="frequency">Frequency : {frequency}</label>
<br />
<input type="range" id="frequency" name="frequency" min="1" max="10" bind:value={frequency} />
<br />
<label for="fps">FPS : {fps}</label>
<br />
<input type="range" id="fps" name="fps" min="1" max="120" bind:value={fps} />
<br />

<h2>TO DO :</h2>
<ul>
	<li>Pouvoir paramétrer toutes les variables</li>
	<li>Pouvoir le faire via l'UI</li>
	<li>Avoir un belle UI</li>
</ul>

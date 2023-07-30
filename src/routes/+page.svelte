<script lang="ts">
	import Connection from '../components/Connection.svelte';

	type Vector2 = [x: number, y: number];

	const dist = ([x1, y1]: Vector2, [x2, y2]: Vector2) => {
		return ((x1 - x2) ** 2 + (y1 - y2) ** 2) ** 0.5;
	};

	const v = 0.5;
	const radius = 30;
	const center1: Vector2 = [230, 230];
	const center2: Vector2 = [330, 330];
	const d = dist(center1, center2);

	const numCirclesX = 6;
	const numCirclesY = 6;

	let centers: Array<Vector2> = [];
	for (let i = 0; i < numCirclesY; i++) {
		let y = 230 + i * 90;
		for (let j = 0; j < numCirclesX; j++) {
			centers.push([230 + j * 90, y]);
		}
	}

	const fill = '#000';
	const stroke = '#000';

	let triggered = false;
	const handleTrigger = () => {
		triggered = !triggered;
		rows = [
			[
				Math.round(Math.random()),
				Math.round(Math.random()),
				Math.round(Math.random()),
				Math.round(Math.random()),
				Math.round(Math.random())
			],
			[
				Math.round(Math.random()),
				Math.round(Math.random()),
				Math.round(Math.random()),
				Math.round(Math.random()),
				Math.round(Math.random())
			],
			[
				Math.round(Math.random()),
				Math.round(Math.random()),
				Math.round(Math.random()),
				Math.round(Math.random()),
				Math.round(Math.random())
			],
			[
				Math.round(Math.random()),
				Math.round(Math.random()),
				Math.round(Math.random()),
				Math.round(Math.random()),
				Math.round(Math.random())
			],
			[
				Math.round(Math.random()),
				Math.round(Math.random()),
				Math.round(Math.random()),
				Math.round(Math.random()),
				Math.round(Math.random())
			]
		];
	};

	let rows = [
		[
			Math.round(Math.random()),
			Math.round(Math.random()),
			Math.round(Math.random()),
			Math.round(Math.random()),
			Math.round(Math.random())
		],
		[
			Math.round(Math.random()),
			Math.round(Math.random()),
			Math.round(Math.random()),
			Math.round(Math.random()),
			Math.round(Math.random())
		],
		[
			Math.round(Math.random()),
			Math.round(Math.random()),
			Math.round(Math.random()),
			Math.round(Math.random()),
			Math.round(Math.random())
		],
		[
			Math.round(Math.random()),
			Math.round(Math.random()),
			Math.round(Math.random()),
			Math.round(Math.random()),
			Math.round(Math.random())
		],
		[
			Math.round(Math.random()),
			Math.round(Math.random()),
			Math.round(Math.random()),
			Math.round(Math.random()),
			Math.round(Math.random())
		]
	];
</script>

<svg id="scroll-container" class="fixed top-0 left-0 h-full w-full" viewBox="0 0 1000 1000">
	<rect width="100%" height="100%" fill="#fff" />
	<g stroke-width="0">
		{#each centers as center, i}
			<circle id={`circle-${i}`} cx={center[0]} cy={center[1]} r={radius} {fill} {stroke} />
		{/each}

		{#each rows as row, i}
			{#each row as square, j}
				<Connection
					center1={centers[(i * 6) + j]}
					center2={centers[(i * 6) + j + 7]}
					show={Boolean(square)}
					arcRadius={2 * radius}
				/>
				<Connection
					center1={centers[(i * 6) + j + 1]}
					center2={centers[(i * 6) + j + 6]}
					show={!Boolean(square)}
					arcRadius={2 * radius}
				/>
			{/each}
		{/each}
	</g>
</svg>

<button class="fixed" on:click={handleTrigger}>trigger</button>

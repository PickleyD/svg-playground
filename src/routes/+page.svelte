<script lang="ts">
	import { onMount } from 'svelte';
	import Connection from '../components/Connection.svelte';
	import Triangle from '../components/Triangle.svelte';
	import { throttle } from 'lodash';
	import { scale, fade } from 'svelte/transition';

	type Vector2 = [x: number, y: number];

	const dist = ([x1, y1]: Vector2, [x2, y2]: Vector2) => {
		return ((x1 - x2) ** 2 + (y1 - y2) ** 2) ** 0.5;
	};

	const v = 0.5;
	const radius = 30;
	const smolRadius = 15;
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

	let edgeCenters: Array<Vector2> = [];
	let edgeVariation = Math.random() > 0.5 ? 0 : 1;
	for (let i = 0; i < numCirclesY * 2 - 1; i++) {
		let y = 230 + i * 45;
		for (let j = 0; j < numCirclesX * 2 - 1; j++) {
			if (i !== 0 && i !== numCirclesX * 2 - 1 - 1 && j !== 0 && j !== numCirclesY * 2 - 1 - 1)
				continue;
			if (edgeVariation) {
				if (i % 4 == 0 && j % 4 == 0) continue;
				if (i % 4 == 2 && j % 4 == 2) continue;
			} else {
				if (i % 4 == 0 && j % 4 == 2) continue;
				if (i % 4 == 2 && j % 4 == 0) continue;
			}
			edgeCenters.push([230 + j * 45, y]);
		}
	}

	let smolCenters: Array<Vector2> = [];
	for (let i = 0; i < numCirclesY * 2 - 1; i++) {
		let y = 230 + i * 45;
		for (let j = 0; j < numCirclesX * 2 - 1; j++) {
			if (i == 0 || j == 0 || i == numCirclesX * 2 - 1 - 1 || j == numCirclesY * 2 - 1 - 1)
				continue;
			if (i % 2 == 1 && j % 2 == 1) continue;
			if (i % 2 == 0 && j % 2 == 0) continue;
			smolCenters.push([230 + j * 45, y]);
		}
	}

	let smolMaybeCenters: Array<Vector2> = [];
	for (let i = 0; i < numCirclesY * 2 - 1; i++) {
		let y = 230 + i * 45;
		for (let j = 0; j < numCirclesX * 2 - 1; j++) {
			if (i == 0 || j == 0 || i == numCirclesX * 2 - 1 - 1 || j == numCirclesY * 2 - 1 - 1)
				continue;
			if (i % 2 == 1 && j % 2 == 1) continue;
			if (!(i % 2 == 0 && j % 2 == 0)) continue;
			if (Math.random() > 0.5) continue;
			smolMaybeCenters.push([230 + j * 45, y]);
		}
	}

	const fill = '#050505';
	const stroke = '#050505';

	const COLORS = [
		'#fb8304',
		'#ef562e',
		'#b3dce0',
		'#f588a3',
		'#27ac9f',
		'#14976b',
		'#2a67af',
		'#f9d432',
		'#abcd5e',
		'#62b6de'
	];

	onMount(() => {
		const onScroll = throttle(handleTrigger, 1500, { leading: true, trailing: false });
		window.addEventListener('wheel', onScroll);

		return () => {
			window.removeEventListener('wheel', onScroll);
		};
	});

	const handleTrigger = () => {
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

		triangleColors = new Array(50).fill(undefined).map(() => COLORS[Math.round(Math.random() * 9)]);

		edgeCenters = [];
		edgeVariation = Math.random() > 0.5 ? 0 : 1;
		for (let i = 0; i < numCirclesY * 2 - 1; i++) {
			let y = 230 + i * 45;
			for (let j = 0; j < numCirclesX * 2 - 1; j++) {
				if (i !== 0 && i !== numCirclesX * 2 - 1 - 1 && j !== 0 && j !== numCirclesY * 2 - 1 - 1)
					continue;
				if (edgeVariation) {
					if (i % 4 == 0 && j % 4 == 0) continue;
					if (i % 4 == 2 && j % 4 == 2) continue;
				} else {
					if (i % 4 == 0 && j % 4 == 2) continue;
					if (i % 4 == 2 && j % 4 == 0) continue;
				}
				edgeCenters.push([230 + j * 45, y]);
			}
		}

		smolMaybeCenters = [];
		for (let i = 0; i < numCirclesY * 2 - 1; i++) {
			let y = 230 + i * 45;
			for (let j = 0; j < numCirclesX * 2 - 1; j++) {
				if (i == 0 || j == 0 || i == numCirclesX * 2 - 1 - 1 || j == numCirclesY * 2 - 1 - 1)
					continue;
				if (i % 2 == 1 && j % 2 == 1) continue;
				if (!(i % 2 == 0 && j % 2 == 0)) continue;
				if (Math.random() > 0.5) continue;
				smolMaybeCenters.push([230 + j * 45, y]);
			}
		}
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

	let triangleColors = new Array(50)
		.fill(undefined)
		.map(() => COLORS[Math.round(Math.random() * 9)]);
</script>

<svg
	id="scroll-container"
	class="bg-[#fefbe6] fixed top-0 left-0 h-full w-full"
	viewBox="0 0 1000 1000"
>
	<rect width="100%" height="100%" fill="#fefbe6" />
	<g stroke-width="0">
		{#each rows as row, i}
			{#each row as square, j}
				{#if Boolean(square)}
					<Triangle
						a={centers[i * 6 + j]}
						b={centers[i * 6 + (j + 1)]}
						c={centers[(i + 1) * 6 + j]}
						fill={triangleColors[i * 5 + j]}
					/>
					<Triangle
						a={centers[(i + 1) * 6 + j]}
						b={centers[i * 6 + (j + 1)]}
						c={centers[(i + 1) * 6 + (j + 1)]}
						fill={triangleColors[i * 5 + j + 1]}
					/>
				{:else}
					<Triangle
						a={centers[i * 6 + j]}
						b={centers[(i + 1) * 6 + j]}
						c={centers[(i + 1) * 6 + (j + 1)]}
						fill={triangleColors[i * 5 + j]}
					/>
					<Triangle
						a={centers[i * 6 + j]}
						b={centers[i * 6 + (j + 1)]}
						c={centers[(i + 1) * 6 + (j + 1)]}
						fill={triangleColors[i * 5 + j + 1]}
					/>
				{/if}
			{/each}
		{/each}

		{#each centers as center, i}
			<circle id={`circle-${i}`} cx={center[0]} cy={center[1]} r={radius} {fill} {stroke} />
		{/each}

		{#each rows as row, i}
			{#each row as square, j}
				<Connection
					center1={centers[i * 6 + j]}
					center2={centers[(i + 1) * 6 + j + 1]}
					show={Boolean(square)}
					arcRadius={2 * radius}
				/>
				<Connection
					center1={centers[i * 6 + j + 1]}
					center2={centers[(i + 1) * 6 + j]}
					show={!Boolean(square)}
					arcRadius={2 * radius}
				/>
			{/each}
		{/each}

		{#each edgeCenters as edgeCenter, i (`${edgeCenter[0]},${edgeCenter[1]}`)}
			<circle
				id={`smol-edge-circle-${i}`}
				cx={edgeCenter[0]}
				cy={edgeCenter[1]}
				r={smolRadius}
				fill="#fefbe6"
				transition:scale={{ duration: 400, opacity: 1 }}
				class="origin-center"
			/>
		{/each}

		{#each smolCenters as smolCenter, i (`${smolCenter[0]},${smolCenter[1]}`)}
			<circle
				id={`smol-circle-${i}`}
				cx={smolCenter[0]}
				cy={smolCenter[1]}
				r={smolRadius}
				fill={'#fefbe6'}
				transition:scale={{ duration: 400, opacity: 1 }}
				class="origin-center"
			/>
		{/each}

		{#each smolMaybeCenters as smolMaybeCenter, i (`${smolMaybeCenter[0]},${smolMaybeCenter[1]}`)}
			<circle
				id={`smol-circle-${i}`}
				cx={smolMaybeCenter[0]}
				cy={smolMaybeCenter[1]}
				r={smolRadius}
				fill={'#fefbe6'}
				transition:scale={{ duration: 400, opacity: 1 }}
				class="origin-center"
			/>
		{/each}
	</g>
</svg>

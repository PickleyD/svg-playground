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
		let y = 230 + i * 100;
		for (let j = 0; j < numCirclesX; j++) {
			centers.push([230 + j * 100, y]);
		}
	}

	const fill = '#000';
	const stroke = '#000';
</script>

<svg class="fixed top-0 left-0 h-full w-full" viewBox="0 0 1000 1000">
	<rect width="100%" height="100%" fill="#fff" />
	<g stroke-width="8">
		{#each centers as center, i}
			<circle id={`circle-${i}`} cx={center[0]} cy={center[1]} r={radius} {fill} {stroke} />
		{/each}

		<Connection center1={centers[0]} center2={centers[7]} fill="black" stroke="black" class=""/>
		<Connection center1={centers[7]} center2={centers[14]} fill="black" stroke="black" class=""/>
		<Connection center1={centers[14]} center2={centers[21]} fill="black" stroke="black" class=""/>
		<Connection center1={centers[14]} center2={centers[19]} fill="black" stroke="black" class=""/>
		<Connection center1={centers[6]} center2={centers[13]} fill="black" stroke="black" class=""/>
		<Connection center1={centers[12]} center2={centers[19]} fill="black" stroke="black" class=""/>

		<Connection center1={centers[15]} center2={centers[16]} fill="black" stroke="black" />
	</g>
</svg>

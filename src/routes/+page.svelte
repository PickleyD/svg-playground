<script lang="ts">
    import Connection from "../components/Connection.svelte"

	type Vector2 = [x: number, y: number];

	const dist = ([x1, y1]: Vector2, [x2, y2]: Vector2) => {
		return ((x1 - x2) ** 2 + (y1 - y2) ** 2) ** 0.5;
	};

	const v = 0.5;
	const radius1 = 30;
	const radius2 = 30;
	const center1: Vector2 = [230, 230];
	const center2: Vector2 = [330, 330];
    const d = dist(center1, center2)

    const fill="#000"
    const stroke="#faa"
</script>

<svg class="fixed top-0 left-0 h-full w-full" viewBox="0 0 1000 1000">
	<rect width="100%" height="100%" fill="#fff" />
	<g stroke-width="8">
		<circle
			id="circle-1"
			cx={center1[0]}
			cy={center1[1]}
			r={radius1}
			{fill}
			{stroke}
		/>

		<circle
			id="circle-2"
			cx={center2[0]}
			cy={center2[1]}
			r={radius2}
			{fill}
			{stroke}
		/>

		<Connection center1={center1} center2={center2} fill='black' stroke='black' />
	</g>
</svg>

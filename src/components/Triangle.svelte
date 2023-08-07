<script lang="ts">
	import { scale, fade } from 'svelte/transition';

	type Vector2 = [x: number, y: number];

	export let a: Vector2;
	export let b: Vector2;
	export let c: Vector2;
	export let fill: string;

	$: path = ['M', a, 'L', b, 'L', c, 'z'].join(' ');
</script>

<path transition:fade={{duration: 400 }} shape-rendering="crispEdges" d={path} {fill} class='transition-colors duration-[400ms]' />

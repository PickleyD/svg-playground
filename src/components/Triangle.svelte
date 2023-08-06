<script lang="ts">
	type Vector2 = [x: number, y: number];

	export let a: Vector2;
	export let b: Vector2;
	export let c: Vector2;
	export let fill: string;
	export let show: boolean = false;

	$: path = ['M', a, 'L', b, 'L', c, 'z'].join(' ');
</script>

{#if show}
	<path shape-rendering="crispEdges" d={path} {fill}/>
{/if}

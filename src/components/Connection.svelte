<script lang="ts">
	import { cn } from '$lib/utils';

	type Vector2 = [x: number, y: number];

	const dist = ([x1, y1]: Vector2, [x2, y2]: Vector2) => {
		return ((x1 - x2) ** 2 + (y1 - y2) ** 2) ** 0.5;
	};

	const angle = ([x1, y1]: Vector2, [x2, y2]: Vector2) => {
		return Math.atan2(y1 - y2, x1 - x2);
	};

	const getVector = ([cx, cy]: Vector2, a: number, r: number): Vector2 => {
		return [cx + r * Math.cos(a), cy + r * Math.sin(a)];
	};

	const metaballToPath = (
		p1: Vector2,
		p2: Vector2,
		p3: Vector2,
		p4: Vector2,
		h1: Vector2,
		h2: Vector2,
		h3: Vector2,
		h4: Vector2,
		escaped: boolean,
		r1: number,
		r2: number
	) => {
		return [
			'M',
			p1,
			'C',
			h1,
			h3,
			p3,
			'A',
			r2,
			r2,
			0,
			escaped ? 1 : 0,
			0,
			p4,
			'C',
			h4,
			h2,
			p2,
			'A',
			r1,
			r1,
			0,
			escaped ? 1 : 0,
			0,
			p1
		].join(' ');
	};

	export let v = 0.5;
	export let handleSize = 2.5;
	export let radius1 = 30;
	export let radius2 = 30;
	export let center1: Vector2;
	export let center2: Vector2;
	export let fill = '#000';
	export let stroke = '#faa';
	const d = dist(center1, center2);
	const maxSpread = Math.acos((radius1 - radius2) / d);
	const angleBetweenCenters = angle(center2, center1);

	const u1 =
		d < radius1 + radius2
			? Math.acos((radius1 * radius1 + d * d - radius2 * radius2) / (2 * radius1 * d))
			: 0;

	const u2 =
		d < radius1 + radius2
			? Math.acos((radius2 * radius2 + d * d - radius1 * radius1) / (2 * radius2 * d))
			: 0;

	const angle1 = angleBetweenCenters + u1 + (maxSpread - u1) * v;
	const angle2 = angleBetweenCenters - (u1 + (maxSpread - u1) * v);
	const angle3 = angleBetweenCenters + Math.PI - u2 - (Math.PI - u2 - maxSpread) * v;
	const angle4 = angleBetweenCenters - (Math.PI - u2 - (Math.PI - u2 - maxSpread) * v);

	// Points
	const p1 = getVector(center1, angle1, radius1);
	const p2 = getVector(center1, angle2, radius1);
	const p3 = getVector(center2, angle3, radius2);
	const p4 = getVector(center2, angle4, radius2);

	const totalRadius = radius1 + radius2;
	// Handle length scaling factor
	const d2 = Math.min(v * handleSize, dist(p1, p3) / totalRadius);
	// Handle lengths
	const r1 = radius1 * d2;
	const r2 = radius2 * d2;

	const HALF_PI = Math.PI / 2;

	const h1 = getVector(p1, angle1 - HALF_PI, r1);
	const h2 = getVector(p2, angle2 + HALF_PI, r1);
	const h3 = getVector(p3, angle3 + HALF_PI, r2);
	const h4 = getVector(p4, angle4 - HALF_PI, r2);

	const path = metaballToPath(p1, p2, p3, p4, h1, h2, h3, h4, d > radius1, radius1, radius2);

	let className: string | undefined | null = undefined;
	export { className as class };
</script>

<path d={path} {fill} {stroke} class={cn('', className)} />

<script lang="ts">
	import { cn } from '$lib/utils';
	import gsap from 'gsap';
	import { MotionPathPlugin } from 'gsap/dist/MotionPathPlugin';
	import { onMount } from 'svelte';

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

	let v = 0.5;
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

	let angle1 = angleBetweenCenters + u1 + (maxSpread - u1) * v;
	let angle2 = angleBetweenCenters - (u1 + (maxSpread - u1) * v);
	let angle3 = angleBetweenCenters + Math.PI - u2 - (Math.PI - u2 - maxSpread) * v;
	let angle4 = angleBetweenCenters - (Math.PI - u2 - (Math.PI - u2 - maxSpread) * v);

	// Points
	let p1 = getVector(center1, angle1, radius1);
	let p2 = getVector(center1, angle2, radius1);
	let p3 = getVector(center2, angle3, radius2);
	let p4 = getVector(center2, angle4, radius2);

	const totalRadius = radius1 + radius2;
	// Handle length scaling factor
	let d2 = Math.min(v * handleSize, dist(p1, p3) / totalRadius);
	// Handle lengths
	let r1 = radius1 * d2;
	let r2 = radius2 * d2;

	const HALF_PI = Math.PI / 2;

	let h1 = getVector(p1, angle1 - HALF_PI, r1);
	let h2 = getVector(p2, angle2 + HALF_PI, r1);
	let h3 = getVector(p3, angle3 + HALF_PI, r2);
	let h4 = getVector(p4, angle4 - HALF_PI, r2);

	let path = metaballToPath(p1, p2, p3, p4, h1, h2, h3, h4, d > radius1, radius1, radius2);

	let className: string | undefined | null = undefined;
	export { className as class };

	//
	let v2 = 0;
	const shrinkAngle1 = angleBetweenCenters + u1 + (maxSpread - u1) * v2;
	const shrinkAngle2 = angleBetweenCenters - (u1 + (maxSpread - u1) * v2);
	const shrinkAngle3 = angleBetweenCenters + Math.PI - u2 - (Math.PI - u2 - maxSpread) * v2;
	const shrinkAngle4 = angleBetweenCenters - (Math.PI - u2 - (Math.PI - u2 - maxSpread) * v2);

	// Points
	const shrinkP1 = getVector(center1, shrinkAngle1, radius1);
	const shrinkP2 = getVector(center1, shrinkAngle2, radius1);
	const shrinkP3 = getVector(center2, shrinkAngle3, radius2);
	const shrinkP4 = getVector(center2, shrinkAngle4, radius2);

	// Handle length scaling factor
	const shrinkD2 = Math.min(v2 * handleSize, dist(shrinkP1, shrinkP3) / totalRadius);
	// Handle lengths
	const shrinkR1 = radius1 * shrinkD2;
	const shrinkR2 = radius2 * shrinkD2;

	const shrinkH1 = getVector(shrinkP1, shrinkAngle1 - HALF_PI, shrinkR1);
	const shrinkH2 = getVector(shrinkP2, shrinkAngle2 + HALF_PI, shrinkR1);
	const shrinkH3 = getVector(shrinkP3, shrinkAngle3 + HALF_PI, shrinkR2);
	const shrinkH4 = getVector(shrinkP4, shrinkAngle4 - HALF_PI, shrinkR2);

	const path2 = metaballToPath(
		shrinkP1,
		shrinkP2,
		shrinkP3,
		shrinkP4,
		shrinkH1,
		shrinkH2,
		shrinkH3,
		shrinkH4,
		d > radius1,
		radius1,
		radius2
	);
	let demo = p1;
	let demoAnim: Vector2 = [0, 0];
	let pxArrAnim: Array<number> = [];
	let pyArrAnim: Array<number> = [];
	let hxArrAnim: Array<number> = [];
	let hyArrAnim: Array<number> = [];

	let arc1 = ['M', p1, 'A', r1, r1, 0, 0, 0, shrinkP1].join(' ');
	let arc2 = ['M', p2, 'A', r1, r1, 0, 0, 1, shrinkP2].join(' ');
	let arc3 = ['M', p3, 'A', r2, r2, 0, 0, 1, shrinkP3].join(' ');
	let arc4 = ['M', p4, 'A', r2, r2, 0, 0, 0, shrinkP4].join(' ');

    let p1Anim = p1
    let p2Anim = p2
    let p3Anim = p3
    let p4Anim = p4

	onMount(() => {
		gsap.registerPlugin(MotionPathPlugin);
		let tl = gsap.timeline({
			repeat: -1
		});

		// tl.to('#test-1', {
		// 	duration: '5',
		// 	motionPath: {
		// 		path: arc1
		// 	}
		// });

        tl.to(p1, {
			duration: 4,
			motionPath: {
				path: arc1
			},
            ease: 'none',
            onUpdate: (() => {
                // @ts-ignore
                p1Anim = [p1.x, p1.y]
            })
		}, 0);

        tl.to(p2, {
			duration: 4,
			motionPath: {
				path: arc2
			},
            ease: 'none',
            onUpdate: (() => {
                // @ts-ignore
                p2Anim = [p2.x, p2.y]
            })
		}, 0);

        tl.to(p3, {
			duration: 4,
			motionPath: {
				path: arc3
			},
            ease: 'none',
            onUpdate: (() => {
                // @ts-ignore
                p3Anim = [p3.x, p3.y]
            })
		}, 0);

        tl.to(p4, {
			duration: 4,
			motionPath: {
				path: arc4
			},
            ease: 'none',
            onUpdate: (() => {
                // @ts-ignore
                p4Anim = [p4.x, p4.y]
            })
		}, 0);

		let pxArr = [p1[0], p2[0], p3[0], p4[0], h1[0], h2[0], h3[0], h4[0]];
		let hxArr = [h1[0], h2[0], h3[0], h4[0]];
		tl.to(
			pxArr,
			{
				endArray: [
					shrinkP1[0],
					shrinkP2[0],
					shrinkP3[0],
					shrinkP4[0],
					shrinkH1[0],
					shrinkH2[0],
					shrinkH3[0],
					shrinkH4[0]
				],
				duration: 4,
				ease: 'sine.out',
				onUpdate: () => {
					pxArrAnim = pxArr;
					// let d2 = Math.min(v * handleSize, dist([pxArr[0], pyArr[0]], [pxArr[2], pyArr[2]]) / totalRadius);
					// // Handle lengths
					// let r1 = radius1 * d2;
					// let r2 = radius2 * d2;

					// let h1 = getVector(p1, angle1 - HALF_PI, r1);
					// let h2 = getVector(p2, angle2 + HALF_PI, r1);
					// let h3 = getVector(p3, angle3 + HALF_PI, r2);
					// let h4 = getVector(p4, angle4 - HALF_PI, r2);
				}
			},
			0
		);

		tl.to(
			hxArr,
			{
				endArray: [shrinkH1[0], shrinkH2[0], shrinkH3[0], shrinkH4[0]],
				duration: 4,
				ease: 'linear',
				onUpdate: () => {
					hxArrAnim = hxArr;
					// let d2 = Math.min(v * handleSize, dist([pxArr[0], pyArr[0]], [pxArr[2], pyArr[2]]) / totalRadius);
					// // Handle lengths
					// let r1 = radius1 * d2;
					// let r2 = radius2 * d2;

					// let h1 = getVector(p1, angle1 - HALF_PI, r1);
					// let h2 = getVector(p2, angle2 + HALF_PI, r1);
					// let h3 = getVector(p3, angle3 + HALF_PI, r2);
					// let h4 = getVector(p4, angle4 - HALF_PI, r2);
				}
			},
			0
		);

		let pyArr = [p1[1], p2[1], p3[1], p4[1], h1[1], h2[1], h3[1], h4[1]];
		let hyArr = [h1[1], h2[1], h3[1], h4[1]];
		tl.to(
			pyArr,
			{
				endArray: [
					shrinkP1[1],
					shrinkP2[1],
					shrinkP3[1],
					shrinkP3[1],
					shrinkH1[1],
					shrinkH2[1],
					shrinkH3[1],
					shrinkH4[1]
				],
				duration: 4,
				ease: 'sine.in',
				onUpdate: () => {
					pyArrAnim = pyArr;
				}
			},
			0
		);
		tl.to(
			hyArr,
			{
				endArray: [shrinkH1[1], shrinkH2[1], shrinkH3[1], shrinkH4[1]],
				duration: 4,
				ease: 'linear',
				onUpdate: () => {
					hyArrAnim = hyArr;
				}
			},
			0
		);

		// gsap.to(opts, {
		//     duration: 4,
		// 	v: 0.4,
		//     ease: 'none',
		// 	onUpdate: function () {
		// 		v = parseFloat(opts.v.toFixed(1))
		// 	}
		// });
	});

	$: escaped = d > radius1;

	$: newPath = [
		'M',
		p1Anim,
		'C',
		// pxArrAnim[4],
		// pyArrAnim[4],
		// pxArrAnim[6],
		// pyArrAnim[6],
		hxArrAnim[0],
		hyArrAnim[0],
		hxArrAnim[2],
		hyArrAnim[2],
		p3Anim,
		'A',
		radius2,
		radius2,
		0,
		escaped ? 1 : 0,
		0,
		p4Anim,
		'C',
		// pxArrAnim[7],
		// pyArrAnim[7],
		// pxArrAnim[5],
		// pyArrAnim[5],
		hxArrAnim[3],
		hyArrAnim[3],
		hxArrAnim[1],
		hyArrAnim[1],
		p2Anim,
		'A',
		radius1,
		radius1,
		0,
		escaped ? 1 : 0,
		0,
		p1Anim
	].join(' ');
</script>

<!-- <path d={arc1} fill="orange" stroke-width="8" stroke="orange" />
<path d={arc2} fill="purple" stroke-width="8" stroke="purple" />
<path d={arc3} fill="green" stroke-width="8" stroke="green" />
<path d={arc4} fill="red" stroke-width="8" stroke="red" /> -->

<circle cx={p1Anim[0]} cy={p1Anim[1]} r={1} fill="cyan" stroke="cyan" />
<circle cx={p2Anim[0]} cy={p2Anim[1]} r={1} fill="cyan" stroke="cyan" />
<circle cx={p3Anim[0]} cy={p3Anim[1]} r={1} fill="cyan" stroke="cyan" />
<circle cx={p4Anim[0]} cy={p4Anim[1]} r={1} fill="cyan" stroke="cyan" />

<!-- <circle cx={h1[0]} cy={h1[1]} r={1} fill="blue" stroke="blue" /> -->
<!-- <circle cx={shrinkH1[0]} cy={shrinkH1[1]} r={1} fill='blue' stroke='blue' /> -->
<!-- <circle cx={260} cy={230} r={1} fill="blue" stroke="blue" />
<circle cx={264.89170033218403} cy={224.15776173636607} r={1} fill="blue" stroke="blue" />
<circle cx={271.5685424949238} cy={230.85786437626905} r={1} fill="blue" stroke="blue" />
<circle cx={277.06787787871883} cy={242.97561263928588} r={1} fill="blue" stroke="blue" />
<circle cx={280} cy={260} r={1} fill="blue" stroke="blue" />
<circle cx={hxArrAnim[0]} cy={hyArrAnim[0]} r={1} fill="purple" stroke="purple" /> -->

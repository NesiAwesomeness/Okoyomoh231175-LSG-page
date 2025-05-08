<script>
	import {
		BufferGeometry,
		Vector3,
		Vector4,
		PointsMaterial,
		BufferAttribute,
		BoxGeometry,
		Points,
		GridHelper,
		Vector2,
		Raycaster,
		PlaneGeometry,
		Mesh,
		SphereGeometry,
		TextureLoader
	} from 'three';

	import { T, useTask, useThrelte } from '@threlte/core';
	import {
		useTexture,
		interactivity,
		Text3DGeometry,
		useGltf,
		OrbitControls
	} from '@threlte/extras';
	import { onMount } from 'svelte';
	import { Spring } from 'svelte/motion';
	import { TextGeometry } from 'three/examples/jsm/Addons.js';

	const cross = useTexture('/cross.png');
	const imagination = useGltf('/glb/Imagination.glb');

	interactivity();

	// Mouse raycasting...
	const { dom, camera, scene } = useThrelte();
	let lastMousePoint = new Vector3();
	let mouseTravel = 0.0;

	let mousePosition = new Vector3(0.1, 0.1, 0.1);
	let mouse = new Vector3(0.1, 0.1, 0.1);
	const planeGeo = new PlaneGeometry(360, 360);
	const mesh = new Mesh(planeGeo);

	const f = new SphereGeometry(0.5);
	const fMesh = new Mesh(f);

	let textGeometry;
	let textPointCount = 1;
	let textPoints = new Float32Array(3);
	let useText = true;

	const particleCount = 4000;

	let pointArray = new Float32Array(particleCount * 3);
	let particleArray = new Float32Array(particleCount * 3);

	let oArray = new Float32Array(particleCount * 3);
	let lorentzArray = new Float32Array(particleCount * 3);
	let lArray = new Float32Array(particleCount * 3);
	let colorArray = new Float32Array(particleCount * 3);

	function handlePointerMove(event) {
		const p = event.intersections[0]?.point;
		if (p) {
			mouse = p;
		}
	}

	const oPoint = new Vector3(10, 10, 10);
	const lorentzPoint = new Vector3(10, 10, 10);
	const lPoint = new Vector3(10, 10, 10);

	// These are the ideal points of the particles.
	// These are the actual points of the particles.

	const pointGeometry = new BufferGeometry();

	let tick = 0.0;
	const gap = 0.2;
	const speed = 4;

	let explosionPoint = new Vector3(0.2, 0.1, 0.8);
	const clamp = (num, min, max) => Math.min(Math.max(num, min), max);

	let t = 0.0;

	const particlePos = new Vector3(1, 1, 1);
	const tempVec = new Vector3(1, 1, 1);
	const tempVec2 = new Vector3();

	const rayDirection = new Vector3(0, 0, 1);
	const closestPointOnRay = new Vector3();
	const direction = new Vector3(1, 0, 1);

	let attractorType = 0;
	let scale = 1.0;
	let begin = 28.0;

	// This should only be calculated for the first point.
	useTask((delta) => {
		const clampedDelta = Math.min(delta, 0.1); // Clamp delta
		begin = Math.max(1.0, begin - clampedDelta * 1.6);
		mousePosition.lerp(mouse, 16.0 * clampedDelta);

		t += delta * 0.1;

		if (t > 2.0) {
			attractorType++;
			if (attractorType > 2) {
				attractorType = 0;
			}
			t = 0.0;
			console.log(attractorType);
		}

		if (particleCount < 1) {
			return;
		}

		fMesh.position.copy(mousePosition);
		mesh.rotation.copy($camera.rotation);

		const cameraPos = $camera.position.clone();
		rayDirection.copy(mousePosition).sub(cameraPos).normalize();

		const intensity = 20.0;
		const maxSpeed = 8.0 * begin;

		const eInfluence = 2.0;
		let speedFactor = 1.0;

		const mouseDelta = mouse.distanceTo(lastMousePoint);
		mouseTravel += mouseDelta * 10.0 * clampedDelta;
		lastMousePoint = mouse;

		for (let i = 0; i < particleCount; i++) {
			const j = i * 3;

			// console.log(mousePosition);

			const x = particleArray[j];
			const y = particleArray[j + 1];
			const z = particleArray[j + 2];

			particlePos.set(x, y, z);

			if (particlePos.lengthSq < 4.0) {
				particlePos.set(Math.random() * 2 - 1, Math.random() * 2 - 1, Math.random() * 2 - 1);
				particlePos.normalize().multiplyScalar(10.0);
			}

			const vectorV = tempVec.copy(particlePos).sub(cameraPos);
			const projectionLength = vectorV.dot(rayDirection);

			closestPointOnRay
				.copy(cameraPos)
				.add(tempVec2.copy(rayDirection).multiplyScalar(projectionLength));

			const mousePoint = mousePosition;
			const distanceToExplosion = particlePos.distanceTo(closestPointOnRay);

			const influence = Math.min((1 / distanceToExplosion) * intensity * mouseTravel, 5.0); // Cap influence
			direction.copy(particlePos).sub(closestPointOnRay).normalize(); // Modifies particlePos!
			// console.log(influence);

			particleArray[j] = x + direction.x * influence;
			particleArray[j + 1] = y + direction.y * influence;
			particleArray[j + 2] = z + direction.z * influence;

			speedFactor = Math.min(1.0, distanceToExplosion / eInfluence);

			for (let b = 0; b < 3; b++) {
				// clamp this value.
				const path = pointArray[j + b] * scale - particleArray[j + b];
				const guide = clamp(path, -maxSpeed, maxSpeed) * speedFactor;

				colorArray[j + b] =
					Math.max(0.0, 1.0 - Math.abs(pointArray[j + b] - particleArray[j + b]) * 0.05) / begin;

				if (b == 2) {
					colorArray[j + b] += 0.2;
				}

				particleArray[j + b] += clampedDelta * guide * (speed * 1.8);
			}
		}

		for (let i = 0; i < speed; i++) {
			// Now this should probably only happen every couple frames.
			// this is called for the particles to then, take the position of the first
			// particle and duplicate through the array.
			tick += delta;
			if (tick > gap / (begin * 0.5)) {
				for (let i = particleCount * 3 - 1; i > 2; i--) {
					lorentzArray[i] = lorentzArray[i - 3];
					oArray[i] = oArray[i - 3];
					lArray[i] = lArray[i - 3];
				}
				tick = 0.0;
			}
			switch (attractorType) {
				case 0: // Lorentz Attractor
					scale = 1.0;
					pointArray = lorentzArray.slice();
					break;
				case 1: // Rossler Attractor
					pointArray = oArray.slice();
					scale = 1.8;
					break;
				case 2: // Langford Attractor
					pointArray = lArray.slice();
					scale = 1.0;
					break;
				default:
					break;
			}

			const odt = 0.02 * begin * clampedDelta;
			const a = 5.0;
			const b = -10.0;
			const d = -0.38;

			oArray[0] = oPoint.x + odt * (a * oPoint.x - oPoint.y * oPoint.z);
			oArray[1] = oPoint.y + odt * (b * oPoint.y + oPoint.x * oPoint.z);
			oArray[2] = oPoint.z + odt * (d * oPoint.z + (oPoint.x * oPoint.y) / 3);

			oPoint.set(oArray[0], oArray[1], oArray[2]);

			const lorentzDt = 0.02 * begin * clampedDelta; // Adjust dt for Lorentz attractor
			const P = 10.0;
			const R = 28.0;
			const B = 8.0 / 3.0;

			lorentzArray[0] = lorentzPoint.x + lorentzDt * (P * (lorentzPoint.y - lorentzPoint.x));
			lorentzArray[1] =
				lorentzPoint.y + lorentzDt * (lorentzPoint.x * (R - lorentzPoint.z) - lorentzPoint.y);
			lorentzArray[2] =
				lorentzPoint.z + lorentzDt * (lorentzPoint.x * lorentzPoint.y - B * lorentzPoint.z);

			lorentzPoint.set(lorentzArray[0], lorentzArray[1], lorentzArray[2]);

			const lDt = 0.02 * clampedDelta * begin;
			const a_dadras = 3.0;
			const b_dadras = 2.7;
			const c_dadras = 1.7;
			const d_dadras = 2.0;
			const e_dadras = 9.0;

			lArray[0] =
				lPoint.x + lDt * (lPoint.y - a_dadras * lPoint.x + b_dadras * lPoint.y * lPoint.z);
			lArray[1] = lPoint.y + lDt * (c_dadras * lPoint.y - lPoint.x * lPoint.z + lPoint.z);
			lArray[2] = lPoint.z + lDt * (d_dadras * lPoint.x * lPoint.y - e_dadras * lPoint.z);

			lPoint.set(lArray[0], lArray[1], lArray[2]);
		}

		pointGeometry.setAttribute('position', new BufferAttribute(particleArray, 3));
		pointGeometry.setAttribute('color', new BufferAttribute(colorArray, 3));

		mouseTravel -= delta * 0.5;

		mouseTravel = clamp(mouseTravel, 0.0, 0.6);
	});
</script>

<T.PerspectiveCamera position={[0, 0, 100]} fov={75} makeDefault near={10}>
	<OrbitControls enableDamping={true} enableZoom={false} />
</T.PerspectiveCamera>

{#await cross then map}
	<T.Points geometry={pointGeometry} position={[0, 0, -12.0]} frustumCulled={false}>
		<T.PointsMaterial size={0.5} {map} transparent vertexColors={true} />
	</T.Points>
{/await}

<T is={mesh} position={[0, 0, 20]} onpointermove={handlePointerMove} onwheel={handlePointerMove}>
	<T.MeshStandardMaterial color={'#001122'} visible={false} />
</T>
<!-- 
{#await imagination then i}
	<T.Mesh geometry={textGeometry} position={[0, 0, -60]} frustumCulled={false} />
{/await} -->

<T is={fMesh} visible={false} />

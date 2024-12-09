<script>
	import { onMount } from "svelte";
	import * as THREE from "three";
	import { FontLoader } from "three/examples/jsm/loaders/FontLoader.js";
	import { TextGeometry } from "three/examples/jsm/geometries/TextGeometry.js";

	let canvas;

	onMount(() => {
		const scene = new THREE.Scene();
		const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
		camera.position.z = 5;

		const renderer = new THREE.WebGLRenderer({ antialias: true, canvas });
		renderer.setSize(window.innerWidth, window.innerHeight);

		// Add ambient light for overall illumination
		const ambientLight = new THREE.AmbientLight(0xffffff, 0.3);
		scene.add(ambientLight);

		// Load font and create text meshes
		const fontLoader = new FontLoader();
		fontLoader.load(
			"https://threejs.org/examples/fonts/helvetiker_regular.typeface.json",
			(font) => {
				// Create ShaderMaterial for glowing effect
				const createMaterial = (baseColor) =>
					new THREE.ShaderMaterial({
						uniforms: {
							cubePosition: { value: new THREE.Vector3(0, 0, 0) },
						},
						vertexShader: `
							varying vec3 vPosition;
							void main() {
								vPosition = (modelMatrix * vec4(position, 1.0)).xyz;
								gl_Position = projectionMatrix * viewMatrix * vec4(vPosition, 1.0);
							}
						`,
						fragmentShader: `
							uniform vec3 cubePosition;
							varying vec3 vPosition;
							void main() {
								float distance = length(cubePosition - vPosition);
								float brightness = 1.0 / (distance * distance + 1.0);
								vec3 glowColor = vec3(${baseColor}) * brightness;
								gl_FragColor = vec4(glowColor, 1.0);
							}
						`,
					});

				const alphabetMaterial = createMaterial("0.553, 0.847, 0.8"); // #8dd8cc
				const alphabetGeometry = new TextGeometry("F", {
					font: font,
					size: 2.5,
					height: 0.2,
				});
				const alphabetMesh = new THREE.Mesh(alphabetGeometry, alphabetMaterial);
				alphabetMesh.position.set(-3, -1, 0); // Left side
				scene.add(alphabetMesh);

				const digitMaterial = createMaterial("0.847, 0.616, 0.553"); // #d89d8d
				const digitGeometry = new TextGeometry("1", {
					font: font,
					size: 2.5,
					height: 0.2,
				});
				const digitMesh = new THREE.Mesh(digitGeometry, digitMaterial);
				digitMesh.position.set(1, -1, 0); // Right side
				scene.add(digitMesh);

				renderLoopMaterials.push(() => {
					alphabetMaterial.uniforms.cubePosition.value.copy(cube.position);
					digitMaterial.uniforms.cubePosition.value.copy(cube.position);
				});
			}
		);

		const cubeGeometry = new THREE.BoxGeometry(0.5, 0.5, 0.5);
		const glowMaterial = new THREE.ShaderMaterial({
			uniforms: {
				time: { value: 0.0 },
			},
			vertexShader: `
				void main() {
					gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
				}
			`,
			fragmentShader: `
				uniform float time;
				void main() {
					float glow = abs(sin(time)) * 0.5 + 0.5;
					gl_FragColor = vec4(vec3(1.0, 1.0, 1.0) * glow, 1.0);
				}
			`,
		});
		const cube = new THREE.Mesh(cubeGeometry, glowMaterial);
		cube.position.set(0, 0, 0);
		scene.add(cube);

		const renderLoopMaterials = [];
		const pointLight = new THREE.PointLight(0xffffff, 1, 10);
		pointLight.position.copy(cube.position);
		scene.add(pointLight);

		const handleKeyDown = (event) => {
			switch (event.key) {
				case "w":
					cube.position.y += 0.1; // Move cube up
					break;
				case "s":
					cube.position.y -= 0.1; // Move cube down
					break;
				case "q":
					cube.position.x -= 0.1; // Move cube left
					break;
				case "e":
					cube.position.x += 0.1; // Move cube right
					break;
				case "a":
					camera.position.x -= 0.1; // Move camera left
					break;
				case "d":
					camera.position.x += 0.1; // Move camera right
					break;
				case "r":
					camera.position.y += 0.1; // Move camera up
					break;
				case "f":
					camera.position.y -= 0.1; // Move camera down
					break;
			}
		};
		window.addEventListener("keydown", handleKeyDown);

		function animate() {
			requestAnimationFrame(animate);
			glowMaterial.uniforms.time.value += 0.05;
			renderLoopMaterials.forEach((update) => update());
			renderer.render(scene, camera);
		}
		animate();

		window.addEventListener("resize", () => {
			camera.aspect = window.innerWidth / window.innerHeight;
			camera.updateProjectionMatrix();
			renderer.setSize(window.innerWidth, window.innerHeight);
		});
	});
</script>

<main>
	<canvas bind:this={canvas}></canvas>
</main>

<style>
	canvas {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
	}
	
</style>

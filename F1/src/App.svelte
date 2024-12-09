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

		// Load font and create text meshes
		const fontLoader = new FontLoader();
		fontLoader.load(
			"https://threejs.org/examples/fonts/gentilis_regular.typeface.json",
			(font) => {
				// Plastic-like Shader for the Alphabet: Lustrous
			const createPlasticMaterial = (baseColor) =>
				new THREE.ShaderMaterial({
					uniforms: {
						cubePosition: { value: new THREE.Vector3(0, 0, 0) },
						ambientIntensity: { value: 0.361 },
						lightPosition: { value: new THREE.Vector3(0, 0, 0) },
						viewPosition: { value: camera.position },
					},
					vertexShader: `
						varying vec3 vNormal;
						varying vec3 vPosition;
						void main() {
							vPosition = (modelMatrix * vec4(position, 1.0)).xyz;
							vNormal = normalize(normalMatrix * normal); // Normalize the normals
							gl_Position = projectionMatrix * viewMatrix * vec4(vPosition, 1.0);
						}
					`,
					fragmentShader: `
						uniform vec3 cubePosition;
						uniform float ambientIntensity;
						uniform vec3 lightPosition;
						uniform vec3 viewPosition;
						varying vec3 vNormal;
						varying vec3 vPosition;

						void main() {
							vec3 lightDir = normalize(lightPosition - vPosition);
							vec3 viewDir = normalize(viewPosition - vPosition);
							vec3 reflectDir = reflect(-lightDir, normalize(vNormal));

							// Ambient
							vec3 ambient = vec3(${baseColor}) * ambientIntensity;

							// Diffuse with distance attenuation, dispersing light over a larger area
							float distance = length(cubePosition - vPosition);
							float attenuation = 1.0 / (distance * distance + 1.0); // Standard attenuation
							attenuation *= 1.5; // Increase light spread by 1.5x
							float diff = max(dot(normalize(vNormal), lightDir), 0.0);
							vec3 diffuse = diff * vec3(${baseColor}) * attenuation;

							// Specular (Lustrous, shiny yet soft appearance)
							float shininess = 50.0; // Moderate shininess for plastic-like highlight
							float spec = pow(max(dot(viewDir, reflectDir), 0.0), shininess);
							vec3 specular = vec3(1.0) * spec * attenuation; // White specular for lustrous effect

							// Final color
							vec3 color = ambient + diffuse + specular;
							gl_FragColor = vec4(color, 1.0);
						}
					`,
				});

				// Metal-like Shader for the Digit: Reflective
				const createMetalMaterial = (baseColor) =>
    new THREE.ShaderMaterial({
        uniforms: {
            cubePosition: { value: new THREE.Vector3(0, 0, 0) },
            ambientIntensity: { value: 0.361 },
            lightPosition: { value: new THREE.Vector3(0, 0, 0) },
            viewPosition: { value: camera.position },
        },
        vertexShader: `
            varying vec3 vNormal;
            varying vec3 vPosition;
            void main() {
                vPosition = (modelMatrix * vec4(position, 1.0)).xyz;
                vNormal = normalize(normalMatrix * normal); // Normalize the normals
                gl_Position = projectionMatrix * viewMatrix * vec4(vPosition, 1.0);
            }
        `,
        fragmentShader: `
            uniform vec3 cubePosition;
            uniform float ambientIntensity;
            uniform vec3 lightPosition;
            uniform vec3 viewPosition;
            varying vec3 vNormal;
            varying vec3 vPosition;

            void main() {
                vec3 lightDir = normalize(lightPosition - vPosition);
                vec3 viewDir = normalize(viewPosition - vPosition);
                vec3 reflectDir = reflect(-lightDir, normalize(vNormal));

                // Ambient
                vec3 ambient = vec3(${baseColor}) * ambientIntensity;

                // Diffuse with distance attenuation
                float distance = length(cubePosition - vPosition);
                float attenuation = 1.0 / (distance * distance + 1.0); // Standard attenuation
                float diff = max(dot(normalize(vNormal), lightDir), 0.0);
                vec3 diffuse = diff * vec3(${baseColor}) * attenuation;

                // Specular (Metal-like reflection)
                float shininess = 300.0; // High shininess for metal appearance
                float spec = pow(max(dot(viewDir, reflectDir), 0.0), shininess);
                vec3 specular = vec3(${baseColor}) * spec * 1.2 * attenuation; // Enhanced specular with base color

                // Final color
                vec3 color = ambient + diffuse + specular;
                gl_FragColor = vec4(color, 1.0);
            }
        `,
    });


				const alphabetMaterial = createPlasticMaterial("0.553, 0.847, 0.8"); // #8dd8cc
				const alphabetGeometry = new TextGeometry("F", {
					font: font,
					size: 2.5,
					height: 0.2,
				});
				const alphabetMesh = new THREE.Mesh(alphabetGeometry, alphabetMaterial);
				alphabetMesh.position.set(-3, -1, 0); // Left side
				scene.add(alphabetMesh);

				const digitMaterial = createMetalMaterial("0.847, 0.616, 0.553"); // #d89d8d
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
					alphabetMaterial.uniforms.lightPosition.value.copy(cube.position);
					digitMaterial.uniforms.cubePosition.value.copy(cube.position);
					digitMaterial.uniforms.lightPosition.value.copy(cube.position);
				});
			}
		);

		const cubeGeometry = new THREE.BoxGeometry(0.3, 0.3, 0.1);
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
					camera.position.x += 0.1; // Move camera left
					break;
				case "d":
					camera.position.x -= 0.1; // Move camera right
					break;
				case "r":
					camera.position.y -= 0.1; // Move camera up
					break;
				case "f":
					camera.position.y += 0.1; // Move camera down
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
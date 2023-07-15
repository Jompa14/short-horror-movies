<template>
<div id="forest-container" ref="forestContainer"></div>
</template>

<script setup>
import * as THREE from 'three';
import {
    OrbitControls
} from 'three/examples/jsm/controls/OrbitControls.js';
import {
    ref,
    onMounted
} from 'vue';

const forestContainer = ref(null);

onMounted(() => {
    // Initialize the Three.js scene
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 100);
    const renderer = new THREE.WebGLRenderer({
        antialias: true
    });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.shadowMap.enabled = true;
    renderer.shadowMap.type = THREE.PCFSoftShadowMap;
    forestContainer.value.appendChild(renderer.domElement);

    // texture loader
    const textureLoader = new THREE.TextureLoader();

    // Load ground texture for the floor
    const groundTexture = textureLoader.load(require('../assets/textures/ground.jpg'));
    groundTexture.wrapS = THREE.RepeatWrapping;
    groundTexture.wrapT = THREE.RepeatWrapping;
    groundTexture.repeat.set(20, 20);
    const groundSize = 200;
    // floor
    const floorGeometry = new THREE.PlaneGeometry(groundSize, groundSize);
    const floorMaterial = new THREE.MeshLambertMaterial({
        map: groundTexture
    });
    const floorMesh = new THREE.Mesh(floorGeometry, floorMaterial);
    floorMesh.receiveShadow = true;
    floorMesh.rotation.x = -Math.PI / 2;
    scene.add(floorMesh);

    // Create a blue moonlight
    const purpleLight = new THREE.PointLight(0x6336e3, 1, 40);
    purpleLight.position.set(5, 2, -1);
    purpleLight.castShadow = true;
    scene.add(purpleLight);

    // Create a cube
    const cubeGeometry = new THREE.ConeGeometry(2, 3, 4);
    const cubeMaterial = new THREE.MeshLambertMaterial({
        color: 'yellow'
    });
    const cubeMesh = new THREE.Mesh(cubeGeometry, cubeMaterial);
    cubeMesh.position.set(5.5, 0, 5);
    cubeMesh.castShadow = true;
    cubeMesh.receiveShadow = true;
    scene.add(cubeMesh);

    // Set up camera position
    camera.position.set(0, 2, 10); // Position the camera to have an overview of the scene

    // Add camera controls
    const controls = new OrbitControls(camera, renderer.domElement);
    controls.enableDamping = true;
    controls.dampingFactor = 0.05;

    //helpers
    // const lightHelper = new THREE.PointLightHelper(purpleLight);
    // scene.add(lightHelper);
    // const gridHelper = new THREE.GridHelper(groundSize, 10, 0x888888, 0x888888);
    // scene.add(gridHelper);

    // Start the animation loop
    function animate() {
        requestAnimationFrame(animate);
        renderer.render(scene, camera);
        controls.update(); // Update camera controls
    }
    animate();
});
</script>

<style scoped>
#forest-container {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
}
</style>

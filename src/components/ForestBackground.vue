<template>
    <div id="forest-container"></div>
</template>

<script>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";

export default {
    mounted() {
        // Initialize the Three.js scene
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(
            75,
            window.innerWidth / window.innerHeight,
            0.1,
            100
        );
        const renderer = new THREE.WebGLRenderer();
        renderer.setSize(window.innerWidth, window.innerHeight);
        document
            .getElementById("forest-container")
            .appendChild(renderer.domElement);

        // texture loader
        const textureLoader = new THREE.TextureLoader();

        // Load ground texture for the floor
        const groundTexture = textureLoader.load(require("../textures/ground.jpg"));
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

        // Create trunk
        const trunkTexture = textureLoader.load(require("../textures/trunk.jpg"));
        const trunkMaterial = new THREE.MeshLambertMaterial({
            map: trunkTexture
        });
        const trunkGeometry = new THREE.CylinderGeometry(0.2, 0.4, 5, 8);
        const trunk = new THREE.Mesh(trunkGeometry, trunkMaterial);
        trunk.position.y = 1;

        // Create foliage
        const foliageTexture = textureLoader.load(require("../textures/leaves.jpg"));
        const foliageGeometry = new THREE.SphereGeometry(3, 8, 6);
        const foliageMaterial = new THREE.MeshLambertMaterial({
            color: 0x478c74,
            map: foliageTexture
        });
        foliageMaterial.map.repeat.set(2, 1.5);
        const foliage = new THREE.Mesh(foliageGeometry, foliageMaterial);
        foliage.position.y = 6.5;

        
        // Create custom tree geometry
        const tree = new THREE.Object3D();
        tree.add(foliage);
        tree.add(trunk);
        tree.position.set(6, 0, 0)
        scene.add(tree);
        
        // Create a blue moonlight
        const purpleLight = new THREE.PointLight(0x6336e3, 1, 40);
        purpleLight.position.set(5, 2, 10);
        scene.add(purpleLight);
        
        // Set up camera position
        camera.position.set(0, 7, 15); // Position the camera to have an overview of the scene

        // Add camera controls
        const controls = new OrbitControls(camera, renderer.domElement);
        controls.enableDamping = true;
        controls.dampingFactor = 0.05;

        // Start the animation loop
        function animate() {
            requestAnimationFrame(animate);
            renderer.render(scene, camera);
            controls.update(); // Update camera controls
        }
        animate();
    }
};
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

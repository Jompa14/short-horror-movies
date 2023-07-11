<template>
  <div id="forest-container"></div>
</template>

<script>
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';

export default {
  mounted() {
    // Initialize the Three.js scene
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 100);
    const renderer = new THREE.WebGLRenderer();
    renderer.setSize(window.innerWidth, window.innerHeight);
    document.getElementById('forest-container').appendChild(renderer.domElement);

    // Create a blue moonlight
    const moonLight = new THREE.PointLight(0x6336e3, 1, 50);
    moonLight.position.set(2, 3, 7);
    scene.add(moonLight);
    // const sphereSize = 1;
    // const pointLightHelper = new THREE.PointLightHelper(moonLight, sphereSize);
    // scene.add(pointLightHelper);

    // Create custom tree geometry
    const trunkGeometry = new THREE.CylinderGeometry(0.2, 0.4, 5, 8);
    const foliageGeometry = new THREE.SphereGeometry(3, 8, 6);

    // Load textures for trunk and foliage
    const textureLoader = new THREE.TextureLoader();
    const trunkTexture = textureLoader.load(require('../textures/trunk.jpg'));
    const foliageTexture = textureLoader.load(require('../textures/leaves.jpg'));

    const trunkMaterial = new THREE.MeshLambertMaterial({ map: trunkTexture });
    const foliageMaterial = new THREE.MeshLambertMaterial({ color: 0x478c74, map: foliageTexture });
    foliageMaterial.map.repeat.set(2, 1.5);

    // Create ground plane
    const groundSize = 200;
    const groundGeometry = new THREE.PlaneGeometry(groundSize, groundSize);
    const groundMaterial = new THREE.MeshLambertMaterial({ color: 0x89a167 });
    const groundMesh = new THREE.Mesh(groundGeometry, groundMaterial);
    groundMesh.receiveShadow = true;
    groundMesh.rotation.x = -Math.PI / 2;
    scene.add(groundMesh);

    // Randomly position trees on the ground
    const numTrees = 300;
    const treePlacementRange = groundSize / 2;
    for (let i = 0; i < numTrees; i++) {
      const tree = new THREE.Object3D();

      // Create trunk
      const trunk = new THREE.Mesh(trunkGeometry, trunkMaterial);
      trunk.position.y = 1;
      tree.add(trunk);

      // Create foliage
      const foliage = new THREE.Mesh(foliageGeometry, foliageMaterial);
      foliage.position.y = 6.5;
      tree.add(foliage);

      // Randomly position the tree on the ground
      const posX = Math.random() * treePlacementRange * 2 - treePlacementRange;
      const posZ = Math.random() * treePlacementRange * 2 - treePlacementRange;
      tree.position.set(posX, 0, posZ);

      scene.add(tree);
    }

    // Set up camera position
    camera.position.set(0, 10, 20); // Position the camera to have an overview of the scene
    camera.lookAt(new THREE.Vector3(0, 0, 0));

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
  },
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

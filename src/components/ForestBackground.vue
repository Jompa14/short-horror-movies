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

    const moonLight = new THREE.PointLight( 0x6336e3, 1, 1000 );
    moonLight.position.set( 2, 2, 7 );
    scene.add( moonLight );
    const sphereSize = 1;
    const pointLightHelper = new THREE.PointLightHelper( moonLight, sphereSize );
    scene.add( pointLightHelper );

    // Grid Helper
    const gridHelper = new THREE.GridHelper(200, 50);
    scene.add(gridHelper);

    // Create custom tree geometry
    const trunkGeometry = new THREE.CylinderGeometry(0.2, 0.4, 5, 8);
    const foliageGeometry = new THREE.SphereGeometry(3, 8, 6);
    const trunkMaterial = new THREE.MeshLambertMaterial({ color: 0x543d2d });
    const foliageMaterial = new THREE.MeshLambertMaterial({ color: 0x008000 });

    // Create tree
    const tree = new THREE.Object3D();

    // Create trunk
    const trunk = new THREE.Mesh(trunkGeometry, trunkMaterial);
    trunk.position.y = 2.5;
    tree.add(trunk);

    // Create foliage
    const foliage = new THREE.Mesh(foliageGeometry, foliageMaterial);
    foliage.position.y = 6.5;
    tree.add(foliage);

    scene.add(tree);

    // Set up camera position
    camera.position.set(0, 2, 10); // Position the camera to look at the tree from the front
    // camera.lookAt(tree.position);

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

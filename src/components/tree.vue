<template>
  <div id="kd-container"></div>
</template>

<script>
import * as THREE from 'three';

export default {
  mounted() {
    // Initialize the Three.js scene
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 100);
    const renderer = new THREE.WebGLRenderer();
    renderer.setSize(window.innerWidth, window.innerHeight);
    document.getElementById('kd-container').appendChild(renderer.domElement);

    // Generate random points
    const numPoints = 50;
    const points = [];
    for (let i = 0; i < numPoints; i++) {
      const x = Math.random() * 10 - 5;
      const y = Math.random() * 10 - 5;
      points.push({ x, y });
    }

    // Create a KD tree from the points
    const kdTree = new THREE.TypedKDTree(points, ['x', 'y']);

    // Visualize the KD tree
    const material = new THREE.PointsMaterial({ color: 0x00ff00 });
    const geometry = new THREE.BufferGeometry();
    const positions = new Float32Array(numPoints * 3);
    for (let i = 0; i < numPoints; i++) {
      const point = points[i];
      positions[i * 3] = point.x;
      positions[i * 3 + 1] = point.y;
      positions[i * 3 + 2] = 0;
    }
    geometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));
    const pointsMesh = new THREE.Points(geometry, material);
    scene.add(pointsMesh);

    // Set up camera position
    camera.position.z = 10;

    // Start the animation loop
    function animate() {
      requestAnimationFrame(animate);
      renderer.render(scene, camera);
    }
    animate();
  },
};
</script>

<style scoped>
#kd-container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
</style>

<template lang="html">
  <div id="tv-room" ref="tvRoomContainer"></div>
</template>

<script setup>
import {
    ref,
    onMounted
} from 'vue';
import * as THREE from 'three';
import {
    RectAreaLightHelper
} from 'three/examples/jsm/helpers/RectAreaLightHelper.js';

const tvRoomContainer = ref(null);

onMounted(() => {
    // Initialize the Three.js scene
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 100);
    const renderer = new THREE.WebGLRenderer();
    renderer.setSize(window.innerWidth, window.innerHeight);
    tvRoomContainer.value.appendChild(renderer.domElement);

    //tv light
    const width = 6;
    const height = 3;
    const intensity = 0;
    const tvLight = new THREE.RectAreaLight(0xffffff, intensity, width, height);
    tvLight.position.set(0, 1, 0);
    tvLight.lookAt(0, 1, 100);
    scene.add(tvLight);
    const rectLightHelper = new RectAreaLightHelper(tvLight);
    tvLight.add(rectLightHelper);

    // purple light
    const purpleLight = new THREE.PointLight(0x6336e3, 1, 40);
    purpleLight.position.set(0, 2, -1);
    purpleLight.castShadow = true;
    scene.add(purpleLight);

    //texture loader
    const textureLoader = new THREE.TextureLoader();

    // hotel floor
    const hotelTexture = textureLoader.load(require('../textures/shinning.jpg'));
    hotelTexture.wrapS = THREE.RepeatWrapping;
    hotelTexture.wrapT = THREE.RepeatWrapping;
    hotelTexture.repeat.set(20, 20);
    const hotelSize = 200;

    const floorGeometry = new THREE.PlaneGeometry(hotelSize, hotelSize);
    const floorMaterial = new THREE.MeshLambertMaterial({
        map: hotelTexture
    });
    const floorMesh = new THREE.Mesh(floorGeometry, floorMaterial);
    floorMesh.receiveShadow = true;
    floorMesh.rotation.x = -Math.PI / 2;
    scene.add(floorMesh);

    camera.position.set(0, 1, 5);

    function animate() {
        purpleLight.power += 0.01;
        tvLight.intensity += 0.009;
        requestAnimationFrame(animate);
        renderer.render(scene, camera);
    }

    animate();
});
</script>

<style scoped>
#tv-room {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
}
</style>

<template>
  <div ref="container" class="canvas-container"></div>
</template>

<script setup>
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
import { ref, onMounted, onBeforeUnmount } from 'vue';

const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(
  75, // field of view, measured in degrees; 75 is a good default which is roughly equivalent to human vision
  window.innerWidth / window.innerHeight, // aspect ratio
  0.1, // view frustum near plane meaning that objects closer than 0.1 units to the camera will not be rendered
  1000 // view frustum far plane meaning that objects further than 1000 units from the camera will not be rendered
);
const renderer = new THREE.WebGLRenderer(); // WebGLRenderer is the renderer class used to render 3D objects to a canvas
// renderer.setPixelRatio(window.devicePixelRatio);
renderer.setSize(window.innerWidth, window.innerHeight);

const container = ref(null);

// const geometry = new THREE.BoxGeometry(1, 1, 1);
// const geometry = new THREE.TorusGeometry(1, 0.2, 16, 100);
const geometry = new THREE.SphereGeometry(1, 32, 16); // parameters: radius, widthSegments, heightSegments
const material = new THREE.MeshStandardMaterial({
  color: 'lightblue',
  // wireframe: true,
});

// Lights
const pointLight = new THREE.PointLight(0xffffff);
pointLight.position.set(2, 2, 2);
scene.add(pointLight);

const ambientLight = new THREE.AmbientLight('navyblue', 0.2);
scene.add(ambientLight);

// Controls
const controls = new OrbitControls(camera, renderer.domElement);

const threeObject = new THREE.Mesh(geometry, material);
scene.add(threeObject);

// camera.position.z = 5;
// camera.position.setZ(30);
camera.position.set(0, 0, 8);

// Helpers
const lightHelper = new THREE.PointLightHelper(pointLight);
scene.add(lightHelper);

const gridHelper = new THREE.GridHelper(200, 50);
scene.add(gridHelper);

const resizeHandler = () => {
  const width = window.innerWidth;
  const height = window.innerHeight;
  renderer.setSize(width, height);
  camera.aspect = width / height;
  camera.updateProjectionMatrix();
};

const animate = () => {
  requestAnimationFrame(animate);
  threeObject.rotation.x += 0.001;
  threeObject.rotation.y += 0.003;

  controls.update();

  renderer.render(scene, camera);
};

onMounted(() => {
  container.value.appendChild(renderer.domElement);
  animate();
  window.addEventListener('resize', resizeHandler);
});

onBeforeUnmount(() => {
  window.removeEventListener('resize', resizeHandler);
});
</script>

<style>
.canvas-container {
  width: 100vw;
  height: 100vh;
  overflow: hidden;
}
</style>

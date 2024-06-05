<template>
  <div ref="container" class="canvas-container"></div>
</template>

<script setup>
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
import { ref, onMounted, onBeforeUnmount } from 'vue';

// EARTH GROUP
const earthGroup = new THREE.Group();
earthGroup.rotation.z = (-23.4 * Math.PI) / 180;

// SCEENE
const scene = new THREE.Scene();
scene.add(earthGroup);

// CAMERA
const camera = new THREE.PerspectiveCamera(
  75, // field of view, measured in degrees; 75 is a good default which is roughly equivalent to human vision
  window.innerWidth / window.innerHeight, // aspect ratio
  0.1, // view frustum near plane meaning that objects closer than 0.1 units to the camera will not be rendered
  1000 // view frustum far plane meaning that objects further than 1000 units from the camera will not be rendered
);

// RENDERER
const renderer = new THREE.WebGLRenderer({ antialias: true }); // WebGLRenderer is the renderer class used to render 3D objects to a canvas
// renderer.setPixelRatio(window.devicePixelRatio);
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.toneMapping = THREE.ACESFilmicToneMapping;
renderer.outputColorSpace = THREE.LinearSRGBColorSpace;

// CONTAINER
const container = ref(null);

// GEOMETRY AND MATERIAL
// const geometry = new THREE.BoxGeometry(1, 1, 1);
// const geometry = new THREE.TorusGeometry(5, 2, 16, 100); // parameters: radius, tube, radialSegments, tubularSegments
// const geometry = new THREE.SphereGeometry(4, 32, 16); // parameters: radius, widthSegments, heightSegments
const geometry = new THREE.IcosahedronGeometry(1, 12); // parameters: radius, detail
const material = new THREE.MeshStandardMaterial({
  // color: 0x3e3ed2,
  // map: new THREE.TextureLoader().load('/images/earthrelief.jpg'),
  // map: new THREE.TextureLoader().load('/images/earthnasa.tif'),
  map: new THREE.TextureLoader().load('/images/earth8k.jpg'),
  // flatShading: true, // flat shading is a technique that makes the faces of a 3D object appear flat with sharp edges
  // wireframe: true,
  // map: new THREE.TextureLoader().load('path/to/texture.jpg'),
});

// OBJECT
const earthMeshObject = new THREE.Mesh(geometry, material);
// scene.add(earthMeshObject);
earthGroup.add(earthMeshObject);

// CONTROLS
const controls = new OrbitControls(camera, renderer.domElement);

// LIGHTS
const lightsMaterial = new THREE.MeshBasicMaterial({
  map: new THREE.TextureLoader().load('/images/earthlights1k.jpg'),
  blending: THREE.AdditiveBlending,
});
const lightsMeshObject = new THREE.Mesh(geometry, lightsMaterial);
earthGroup.add(lightsMeshObject);

// const pointLight = new THREE.PointLight(0xffffff, 40); // color, intensity, distance
const pointLight = new THREE.DirectionalLight(0xffffff, 7); // color, intensity
pointLight.position.set(10, 2, 0);
scene.add(pointLight);

// const ambientLight = new THREE.AmbientLight(0xffffff); // color, intensity // 0xa3b899
// scene.add(ambientLight);

// CAMERA
// camera.position.z = 5;
// camera.position.setZ(30);
camera.position.set(-3, 3, 5);

// Helpers
const lightHelper = new THREE.PointLightHelper(pointLight);
scene.add(lightHelper);

const gridHelper = new THREE.GridHelper(200, 50);
scene.add(gridHelper);

// Adding multiple stars to the scene
const addStar = () => {
  const geometry = new THREE.SphereGeometry(0.25, 24, 24);
  const material = new THREE.MeshStandardMaterial({ color: 'white' });
  const star = new THREE.Mesh(geometry, material);

  const [x, y, z] = Array(3)
    .fill()
    .map(() => THREE.MathUtils.randFloatSpread(100));

  star.position.set(x, y, z);
  scene.add(star);
};
Array(200).fill().forEach(addStar);

// Resize handler; this is necessary to make the canvas responsive
const resizeHandler = () => {
  const width = window.innerWidth;
  const height = window.innerHeight;
  renderer.setSize(width, height);
  camera.aspect = width / height;
  camera.updateProjectionMatrix();
};

// ANIMATION LOOP
const animate = () => {
  requestAnimationFrame(animate);
  earthMeshObject.rotation.y += 0.003; // This is the rotation speed of the object, on the y-axis, measured in radians
  lightsMeshObject.rotation.y += 0.003;
  // pointLight.position.x -= 0.01;

  controls.update();

  renderer.render(scene, camera);
};

// MOUNTING
onMounted(() => {
  container.value.appendChild(renderer.domElement);
  animate();
  window.addEventListener('resize', resizeHandler);
});

// UNMOUNTING
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

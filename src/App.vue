<script setup>
import * as THREE from "three";
import { onMounted, ref } from "vue";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / 500,
  0.1,
  1000
);
const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, 500);
const container = ref(null);
const geometry = new THREE.BoxGeometry(2, 2, 2);
const material = new THREE.MeshPhongMaterial({ color: 0x00ff00 });
const cube = new THREE.Mesh(geometry, material);

// scene.add(cube);
camera.position.z = 5;
const ambientLight = new THREE.AmbientLight(0xffffff, 0.1);
scene.add(ambientLight);
const light = new THREE.PointLight(0xffffff, 100, 100);
light.position.set(50, 50, 50);
scene.add(light);

const gltfLoader = new GLTFLoader();

let myGltf = null;
gltfLoader.load("/src/assets/workspace_final.gltf", (gltfScene) => {
  gltfScene.scene.rotation.y = Math.PI / 8;
  gltfScene.scene.position.y = -3;
  gltfScene.scene.scale.set(3, 3, 3);
  myGltf = gltfScene.scene;
  console.log(gltfScene);
  scene.add(gltfScene.scene);
});

const textureLoader = new THREE.TextureLoader();
textureLoader.load("/src/assets/87.jpg", function (texture) {
  const materialA = new THREE.MeshPhongMaterial({ map: texture });
  cube.material = materialA;
});
const animate = () => {
  requestAnimationFrame(animate);
  // cube.rotation.y += 0.01;
  renderer.render(scene, camera);
};

let previousPosition = { x: 0, y: 0 };
onMounted(() => {
  container.value.appendChild(renderer.domElement);
  container.value.addEventListener("mousedown", mouseDown);
  container.value.addEventListener("mouseup", mouseUp);
  container.value.addEventListener("mousemove", mouseMove);
  animate();

  let zoom = 1;
  // 滑鼠滾輪事件
  const handleMouseWheel = (event) => {
    console.log("dsfs");
    event.preventDefault();
    const delta = Math.sign(event.deltaY); // 滾輪方向，上為正數，下為負數
    const scaleSpeed = 0.1; // 縮放速度

    // 更新縮放
    zoom -= delta * scaleSpeed;

    // 設定相機的縮放
    camera.zoom = zoom;
    camera.updateProjectionMatrix();
  };

  // 添加滑鼠滾輪監聽事件
  container.value.addEventListener("wheel", handleMouseWheel, {
    passive: false,
  });
});

let isDragging = false;

const mouseDown = (event) => {
  isDragging = true;
  const { clientX, clientY } = event;
  previousPosition = { x: clientX, y: clientY };
};
const mouseUp = (event) => {
  isDragging = false;
};
const mouseMove = (event) => {
  if (!isDragging) return;

  const { clientX, clientY } = event;
  const movementX = clientX - previousPosition.x;
  const movementY = clientY - previousPosition.y;
  const rotationSpeed = 0.01;
  // cube.rotation.y += movementX * rotationSpeed;
  // cube.rotation.x += movementY * rotationSpeed;
  myGltf.rotation.y += movementX * rotationSpeed;
  myGltf.rotation.x += movementY * rotationSpeed;
  previousPosition = { x: clientX, y: clientY };
};
</script>

<template>
  <div ref="container"></div>
</template>

<style scoped></style>

<template></template>
<script setup lang="ts">
import {
  Mesh,
  Shape,
  Scene,
  Color,
  Group,
  WebGLRenderer,
  BoxGeometry,
  ExtrudeGeometry,
  DirectionalLight,
  MeshPhongMaterial,
  PerspectiveCamera,
} from "three";
import { onMounted, onUpdated } from "vue";

const scene = new Scene();
scene.background = new Color(0x444444);

const renderer = new WebGLRenderer({ antialias: true });
renderer.setPixelRatio(window.devicePixelRatio);
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

const camera = new PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  50
);
camera.position.z = 35;

// 图形配置
const extrudeSettings = {
  steps: 9,
  depth: 9,
  bevelEnabled: true,
  bevelThickness: 1,
  bevelSize: 1,
  bevelOffset: -1,
  bevelSegments: 7,
};

const length = 9,
  width = 9;

const shape = new Shape();
shape.moveTo(0, 0);
shape.lineTo(0, width);
shape.lineTo(length, width);
shape.lineTo(length, 0);
shape.lineTo(0, 0);

const group = new Group();

// const geometry = new ExtrudeGeometry(shape, extrudeSettings);
const geometry = new BoxGeometry(10, 10, 10);

geometry.center();
// 光点
var light = new DirectionalLight(0xf39048, 0.8);
light.position.set(20, 30, 60);
light.target.position.set(0, 0, 20);
light.castShadow = true;
// these six values define the boundaries of the yellow box seen above
light.shadow.camera.near = 2;
light.shadow.camera.far = 5;
light.shadow.camera.left = -0.5;
light.shadow.camera.right = 0.5;
light.shadow.camera.top = 0.5;
light.shadow.camera.bottom = -0.5;
// 加光源
scene.add(light);

// 矩形纹理
const meshMaterial = new MeshPhongMaterial({
  color: 0xf5f5f5,
  // emissive: 0xe9eaed, // 材质的放射（光）颜色，基本上是不受其他光照影响的固有颜色。默认为黑色。
  shininess: 10,
});

// 整合纹理
group.add(new Mesh(geometry, meshMaterial));
scene.add(group);

function animate() {
  requestAnimationFrame(animate);

  group.rotation.x += 0.01;
  group.rotation.y += 0.01;

  renderer.render(scene, camera);
}

window.addEventListener(
  "resize",
  function () {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();

    renderer.setSize(window.innerWidth, window.innerHeight);
  },
  false
);

onMounted(() => {
  animate();
});
onUpdated(() => {
  renderer.render(scene, camera);
});
</script>

<template></template>
<script setup lang="ts">
import {
  Mesh,
  Shape,
  Scene,
  Color,
  Group,
  DoubleSide,
  LineSegments,
  WebGLRenderer,
  ExtrudeGeometry,
  LineBasicMaterial,
  MeshPhongMaterial,
  PerspectiveCamera,
} from "three";

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
camera.position.z = 30;

// 图形配置
const extrudeSettings = {
  steps: 4,
  depth: 19,
  bevelEnabled: true,
  bevelThickness: 1,
  bevelSize: 1,
  bevelOffset: -1,
  bevelSegments: 7,
};

const length = 10,
  width = 8;

const shape = new Shape();
shape.moveTo(0, 0);
shape.lineTo(0, width);
shape.lineTo(length, width);
shape.lineTo(length, 0);
shape.lineTo(0, 0);

const group = new Group();

const geometry = new ExtrudeGeometry(shape, extrudeSettings);
geometry.center();

// 线性纹理
const lineMaterial = new LineBasicMaterial({
  color: 0xffffff,
  transparent: true,
  opacity: 0.5,
});
// 矩形纹理
const meshMaterial = new MeshPhongMaterial({
  color: 0x156289,
  emissive: 0x072534,
  side: DoubleSide,
  flatShading: false,
});

// 整合图形和纹理
group.add(new LineSegments(geometry, lineMaterial));
group.add(new Mesh(geometry, meshMaterial));
scene.add(group);

function animate() {
  requestAnimationFrame(animate);

  group.rotation.x += 0.005;
  group.rotation.y += 0.005;

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

animate();
</script>

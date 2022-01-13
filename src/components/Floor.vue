<template></template>
<script setup lang="ts">
import {
  Mesh,
  Shape,
  Scene,
  Color,
  Group,
  Texture,
  AxesHelper,
  BoxGeometry,
  CameraHelper,
  AmbientLight,
  TextureLoader,
  WebGLRenderer,
  RepeatWrapping,
  ExtrudeGeometry,
  DirectionalLight,
  PerspectiveCamera,
  MeshLambertMaterial,
} from "three";
import { onMounted, onUpdated } from "vue";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
const props = defineProps({
  innerWidth: {
    type: Number,
    default: 100,
  },
  innerHeight: {
    type: Number,
    default: 100,
  },
});

const scene = new Scene();

//配置相机
var camera: PerspectiveCamera;
function initCamera() {
  camera = new PerspectiveCamera(45, innerWidth / innerHeight, 0.1, 2000);
  // 视角
  camera.position.set(100, 300, (innerWidth * 2) / 3);
  // const helper = new CameraHelper(camera);
  // scene.add(helper);
}
initCamera();

var axes: AxesHelper;
//添加坐标轴
scene.add((axes = new AxesHelper(320)));

//设置贴图
function initTexture() {
  var floorTexture: TextureLoader = new TextureLoader();
  floorTexture.load("/src/assets/img/floor.jpeg", (texture: Texture) => {
    texture.wrapS = texture.wrapT = RepeatWrapping;
    texture.repeat.set(14, 10);
    initFloor(texture);
    render();
  });
}
initTexture();

// 地板
var floor;
function initFloor(texture: Texture) {
  var geometry = new BoxGeometry(500, 5, 500);
  var material = new MeshLambertMaterial({
    color: 0xffffff,
    map: texture,
  });
  floor = new Mesh(geometry, material);
  floor.position.set(150, -10, 100);
  scene.add(floor);
}

//配置光源
var dircLight;
function initLight() {
  dircLight = new DirectionalLight(0xffffff);
  dircLight.position.set(300, 400, 300);
  scene.add(dircLight);
  scene.add(new AmbientLight(0x444444));
}
initLight();

//设置渲染器
var renderer: WebGLRenderer;
function initRenderer() {
  renderer = new WebGLRenderer({
    antialias: true, //抗锯齿:true
  });
  renderer.setSize(innerWidth, innerHeight); //画布尺寸
  renderer.setClearColor("#3e93dd"); //背景色
  document.body.appendChild(renderer.domElement);
}
initRenderer();

//视角控制器
var controls;
function ctrl() {
  controls = new OrbitControls(camera, renderer.domElement);
  controls.addEventListener("change", render);
  //相机到原点的最远距离
  controls.maxDistance = 2000;
}
ctrl();

function render() {
  renderer.render(scene, camera);
}

function onResize() {
  if (camera && renderer) {
    // aspect表示屏幕长宽比
    camera.aspect = innerWidth / innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(innerWidth, innerHeight);
  }
}

function onDocumentMouseDown(event: Event) {
  event.preventDefault();
  render();
}

onMounted(() => {
  // 浏览器变化时画布自适应
  window.addEventListener("resize", onResize, false);
  document.addEventListener("dblclick", onDocumentMouseDown, false);
});

onUpdated(() => {});
</script>

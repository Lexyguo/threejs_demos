<template></template>
<script setup lang="ts">
import {
  Mesh,
  Scene,
  Texture,
  Vector3,
  Raycaster,
  AxesHelper,
  BoxGeometry,
  CameraHelper,
  AmbientLight,
  TextureLoader,
  WebGLRenderer,
  RepeatWrapping,
  DirectionalLight,
  PerspectiveCamera,
  MeshLambertMaterial,
} from "three";
import { onMounted, onUpdated } from "vue";
import * as refrigerator from "./Refrigerator.vue";
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
  camera.position.set(100, 300, 1000);
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
    texture.repeat.set(40, 40);
    initFloor(texture);
    render();
  });
}
initTexture();

// 地板
var floor;
function initFloor(texture: Texture) {
  var geometry = new BoxGeometry(10000, 5, 10000);
  var material = new MeshLambertMaterial({
    color: 0xffffff,
    map: texture,
  });
  floor = new Mesh(geometry, material);
  floor.position.set(150, -5, 200);
  scene.add(floor);
}

const { refrigeratorGroup, door } = refrigerator.init(0, 0, 0, 0xffffff);
scene.add(refrigeratorGroup);

//配置光源
var dircLight;
function initLight() {
  dircLight = new DirectionalLight(0xffffff);
  dircLight.position.set(800, 1000, 1000);
  scene.add(dircLight);
  scene.add(new AmbientLight(0xffffff, 0.4));
}
initLight();

//设置渲染器
var renderer: WebGLRenderer;
function initRenderer() {
  renderer = new WebGLRenderer({
    antialias: true, //抗锯齿:true
  });
  renderer.setSize(innerWidth, innerHeight); //画布尺寸
  renderer.setClearColor("#030f2e"); //背景色
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

function onMouseDown(event: MouseEvent) {
  // console.log(event);
  let vector = new Vector3( // 将鼠标位置归一化为设备坐标。x 和 y 方向的取值范围是 (-1 to +1)
    (event.clientX / window.innerWidth) * 2 - 1,
    -(event.clientY / window.innerHeight) * 2 + 1,
    1
  );
  vector = vector.unproject(camera);
  const raycaster = new Raycaster( // 通过摄像机和鼠标位置更新射线
    camera.position,
    vector.sub(camera.position).normalize()
  );

  // 计算物体和射线的交点
  const intersects = raycaster.intersectObjects([door.door]);
  if (intersects.length > 0) {
    door.doorAnimate();
    render();
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
  //给window绑定 开门点击事件
  window.addEventListener("click", onMouseDown);
});

onUpdated(() => {});
</script>

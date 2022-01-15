<script lang="ts">
import {
  Mesh,
  Scene,
  Group,
  Clock,
  Texture,
  BoxGeometry,
  TextureLoader,
  AnimationClip,
  KeyframeTrack,
  AnimationMixer,
  MeshBasicMaterial,
  MeshLambertMaterial,
  MeshPhysicalMaterial,
} from "three";
import { CSG } from "three-csg-ts";

export function init(
  x: number,
  y: number,
  z: number,
  themeColor: number,
  scene: Scene,
  render: Function
) {
  // 创建时钟
  const clock = new Clock();

  var innnerX = x;
  var innnerY = y;
  var innnerZ = z;
  var themeColor = themeColor;

  var dw = 50; // 柜体外沿长
  var dd = 50; // 柜体外沿宽(深)
  var dh = 166; // 柜体外沿高

  // 设置机箱的外壳
  var refrigeratorMatLambert: MeshLambertMaterial; // 机柜纹理
  var refrigeratorMatBasic: MeshBasicMaterial; // 机柜外壳基础
  var refrigeratorzMat: Mesh;

  var refrigeratorGroup = new Group();
  // refrigeratorGroup的平面中心是机柜主体的平面中心
  refrigeratorGroup.position.set(innnerX, innnerY, innnerZ);
  refrigeratorGroup.name = "refrigeratorGroup";

  // 正视图
  var frontTexture: TextureLoader = new TextureLoader();
  frontTexture.load(
    "/src/assets/img/refrigerator_front.png",
    (texture: Texture) => {
      var doorGroup = new Group();
      doorGroup.position.set(innnerX, innnerY, innnerZ);

      // 正前方顶部广告位
      var topAdGeo = new BoxGeometry(dw, 30, 4); // 柜子门宽，高，厚
      var topAd = new Mesh(
        topAdGeo,
        new MeshLambertMaterial({ color: themeColor })
      );
      topAd.name = "Top Ad";
      topAd.position.set(dw / 2, dh - 15, dw);
      doorGroup.add(topAd);

      //设置柜体门
      var doorGeo = new BoxGeometry(dw, 100, 4); // 柜子门宽，高，厚
      // 门框
      var doorFrame = new Mesh(
        doorGeo,
        new MeshLambertMaterial({ color: themeColor }) // 门框纹理
      );
      doorFrame.name = "Door Frame";
      doorFrame.position.set(dw / 2, dh - 82, dw);
      // 为了做门的几何运算（并集）
      doorFrame.updateMatrix();

      // 玻璃部分
      var grassGeo = new BoxGeometry(dw - 4, 92, 4); // 柜子玻璃部宽，高，厚
      var grass = new Mesh(
        grassGeo,
        new MeshPhysicalMaterial({
          map: null,
          color: 0xcfcfcf,
          metalness: 0,
          roughness: 0,
          opacity: 0.45,
          transparent: true,
          envMapIntensity: 10,
          premultipliedAlpha: true,
        })
      );
      grass.name = "Door Frame Grass";
      grass.position.set(dw / 2, dh - 82, dw);
      // 为了做门的几何运算（并集）
      grass.updateMatrix();
      var door = CSG.subtract(doorFrame, grass);
      door.position.x = dw;
      door.position.z = (dd * 3) / 2;
      door.rotation.y = -Math.PI / 2;
      doorGroup.add(door);

      // 正前方底部广告位
      var bottomAdGeo = new BoxGeometry(dw, 30, 4); // 柜子门宽，高，厚
      var bottomAd = new Mesh(
        bottomAdGeo,
        new MeshLambertMaterial({ color: themeColor })
      );
      bottomAd.name = "Top Ad";
      bottomAd.position.set(dw / 2, dh - 150, dw);
      doorGroup.add(bottomAd);

      refrigeratorGroup.add(doorGroup);
      render();
    }
  );

  // 机身
  var bodyGeo = new BoxGeometry(dw, dh, dd); // 柜子门宽，高，厚
  var body = new Mesh(bodyGeo, new MeshLambertMaterial({ color: themeColor }));
  body.name = "Refrigerator body";
  body.position.set(dw / 2, dh / 2, dd / 2);
  // 为了机身内部的几何运算（并集）
  body.updateMatrix();

  // 机身内部空间
  var bodyInsideGeo = new BoxGeometry(dw - 4, 92, dd - 8); // 柜子门宽，高，厚
  var bInsideMaterials = [];
  bInsideMaterials.push(
    new MeshLambertMaterial({
      color: 0xffffff,
      opacity: 0.9,
      transparent: true,
    }),
    new MeshLambertMaterial({
      color: 0xffffff,
      opacity: 0.9,
      transparent: true,
    }),
    new MeshLambertMaterial({
      color: 0xffffff,
      opacity: 0.9,
      transparent: true,
    }),
    new MeshLambertMaterial({
      color: 0xffffff,
      opacity: 0.9,
      transparent: true,
    }),
    new MeshLambertMaterial({
      color: 0xffffff,
      opacity: 0.9,
      transparent: true,
    }), // 玻璃质感
    new MeshLambertMaterial({
      color: 0xffffff,
      opacity: 0.9,
      transparent: true,
    }) // 玻璃质感
  );
  var bodyInside = new Mesh(bodyInsideGeo, bInsideMaterials);
  bodyInside.name = "Refrigerator body inside";
  bodyInside.position.set(dw / 2, dh / 2, dd / 2 + 4);
  // 为了机身内部的几何运算（并集）
  bodyInside.updateMatrix();

  var refrigeratorBody = CSG.subtract(body, bodyInside);
  refrigeratorGroup.add(refrigeratorBody);

  scene.add(refrigeratorGroup);
}
</script>

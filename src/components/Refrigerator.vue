<script lang="ts">
import {
  Mesh,
  Group,
  Clock,
  BoxGeometry,
  MeshBasicMaterial,
  MeshPhongMaterial,
  MeshLambertMaterial,
} from "three";
import { CSG } from "three-csg-ts";

class Door {
  public status: string;
  public door: any;
  private dw: number;
  private dh: number;
  private dd: number;
  private themeColor: number;

  constructor(
    dw: number,
    dh: number,
    dd: number,
    themeColor: number,
    isOpenDoor?: Boolean
  ) {
    this.status = !isOpenDoor ? "closed" : "opened";
    this.dw = dw;
    this.dh = dh;
    this.dd = dd;
    this.themeColor = themeColor;
    this.initDoor(dw, dh, themeColor);
  }

  private initDoor(dw: number, dh: number, themeColor: number) {
    //设置柜体门
    var doorGeo = new BoxGeometry(dw, 200, 8); // 柜子门宽，高，厚
    // 门框
    var doorFrame = new Mesh(
      doorGeo,
      new MeshLambertMaterial({ color: themeColor }) // 门框纹理
    );
    doorFrame.name = "Door Frame";
    doorFrame.position.set(dw / 2, dh - 164, dw);
    // 为了做门的几何运算（并集）
    doorFrame.updateMatrix();

    // 玻璃部分
    var glassGeo = new BoxGeometry(dw - 16, 184, 8); // 柜子玻璃部宽，高，厚
    var glass = new Mesh(
      glassGeo,
      new MeshPhongMaterial({
        color: 0xffffff,
        refractionRatio: 0.8,
      })
    );
    glass.name = "Door Frame glass";
    glass.position.set(dw / 2, dh - 164, dw);
    // 为了做门的几何运算（并集）
    glass.updateMatrix();
    this.door = CSG.subtract(doorFrame, glass);
  }

  public doorAnimate() {
    if (this.status === "closed") {
      this.door.position.x = this.dw;
      this.door.position.z = (this.dd * 3) / 2;
      this.door.rotation.y = -Math.PI / 2;
      this.status = "opened";
    } else {
      this.door.position.x = this.dw / 2;
      this.door.position.z = this.dd;
      this.door.rotation.y = 0;
      this.status = "closed";
    }
  }
}

export function init(x: number, y: number, z: number, themeColor: number) {
  // 创建时钟
  const clock = new Clock();

  var innnerX = x;
  var innnerY = y;
  var innnerZ = z;
  var themeColor = themeColor;

  var dw = 100; // 柜体外沿长
  var dd = 100; // 柜体外沿宽(深)
  var dh = 332; // 柜体外沿高

  // 设置机箱的外壳
  var refrigeratorMatLambert: MeshLambertMaterial; // 机柜纹理
  var refrigeratorMatBasic: MeshBasicMaterial; // 机柜外壳基础
  var refrigeratorzMat: Mesh;

  var refrigeratorGroup = new Group();
  // refrigeratorGroup的平面中心是机柜主体的平面中心
  refrigeratorGroup.position.set(innnerX, innnerY, innnerZ);
  refrigeratorGroup.name = "refrigeratorGroup";

  // 正视图
  var frontGroup = new Group();
  frontGroup.position.set(innnerX, innnerY, innnerZ);

  // 正前方顶部广告位
  var topAdGeo = new BoxGeometry(dw, 60, 8); // 柜子门宽，高，厚
  var topAd = new Mesh(
    topAdGeo,
    new MeshLambertMaterial({ color: themeColor })
  );
  topAd.name = "Top Ad";
  topAd.position.set(dw / 2, dh - 30, dw);
  frontGroup.add(topAd);

  // 门
  const door = new Door(dw, dh, dd, themeColor);
  frontGroup.add(door.door);

  // 正前方底部广告位
  var bottomAdGeo = new BoxGeometry(dw, 60, 8); // 柜子门宽，高，厚
  var bottomAd = new Mesh(
    bottomAdGeo,
    new MeshLambertMaterial({ color: themeColor })
  );
  bottomAd.name = "Top Ad";
  bottomAd.position.set(dw / 2, dh - 300, dw);
  frontGroup.add(bottomAd);

  refrigeratorGroup.add(frontGroup);

  // 机身
  var bodyGeo = new BoxGeometry(dw, dh, dd); // 柜子门宽，高，厚
  var body = new Mesh(bodyGeo, new MeshLambertMaterial({ color: themeColor }));
  body.name = "Refrigerator body";
  body.position.set(dw / 2, dh / 2, dd / 2);
  // 为了机身内部的几何运算（并集）
  body.updateMatrix();

  // 机身内部空间
  var bodyInsideGeo = new BoxGeometry(dw - 16, 184, dd - 16); // 柜子门宽，高，厚
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
  bodyInside.position.set(dw / 2, dh / 2, dd / 2 + 8);
  // 为了机身内部的几何运算（并集）
  bodyInside.updateMatrix();

  var refrigeratorBody = CSG.subtract(body, bodyInside);
  refrigeratorGroup.add(refrigeratorBody);

  return { refrigeratorGroup, door };
}
</script>

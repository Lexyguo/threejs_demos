<script lang="ts">
import {
  Mesh,
  Group,
  Clock,
  BoxGeometry,
  MeshBasicMaterial,
  MeshLambertMaterial,
  MeshPhysicalMaterial,
  RepeatWrapping,
  Texture,
  TextureLoader,
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
    // 门整体
    var doorPart = new Mesh(
      doorGeo,
      new MeshLambertMaterial({ color: themeColor }) // 门框纹理
    );
    doorPart.name = "Door Frame";
    doorPart.position.set(dw / 2, dh - 164, dw);
    // 为了做门的几何运算（并集）
    doorPart.updateMatrix();

    // 玻璃镂空部分
    var glassPartGeo = new BoxGeometry(dw - 16, 184, 8); // 柜子玻璃部宽，高，厚
    var glassPart = new Mesh(
      glassPartGeo,
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
    glassPart.name = "Door Frame glass part";
    glassPart.position.set(dw / 2, dh - 164, dw);
    // 为了做门的几何运算（并集）
    glassPart.updateMatrix();

    // 门框
    this.door = CSG.subtract(doorPart, glassPart);

    // 门上玻璃部分
    var glassGeo = new BoxGeometry(dw - 16, 184, 8); // 柜子玻璃部宽，高，厚
    var glass = new Mesh(
      glassGeo,
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
    glass.name = "Door Frame glass";
    // 加上玻璃后的门
    this.door.add(glass);
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
  var topAdTexture: TextureLoader = new TextureLoader();
  topAdTexture.load(
    "https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fwww.designhill.com%2Fdesign-blog%2Fwp-content%2Fuploads%2F2019%2F04%2F9-min-3.jpg&refer=http%3A%2F%2Fwww.designhill.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1645606909&t=6d48a72f96a0d29fbace8124d7701e4b",
    (texture: Texture) => {
      texture.wrapS = texture.wrapT = RepeatWrapping;
      var topAdGeo = new BoxGeometry(dw, 60, 8); // 柜子门宽，高，厚
      var topAd = new Mesh(topAdGeo, [
        new MeshLambertMaterial({ color: themeColor }),
        new MeshLambertMaterial({ color: themeColor }),
        new MeshLambertMaterial({ color: themeColor }),
        new MeshLambertMaterial({ color: themeColor }),
        new MeshLambertMaterial({ color: themeColor, map: texture }),
        new MeshLambertMaterial({ color: themeColor }),
      ]);
      topAd.name = "Top Ad";
      topAd.position.set(dw / 2, dh - 30, dw);

      frontGroup.add(topAd);
    }
  );

  // 门
  const door = new Door(dw, dh, dd, themeColor);
  frontGroup.add(door.door);

  // 正前方底部广告位
  var bottomAdTexture: TextureLoader = new TextureLoader();
  bottomAdTexture.load(
    "https://gimg2.baidu.com/image_search/src=http%3A%2F%2Finews.gtimg.com%2Fnewsapp_match%2F0%2F7914776942%2F0.jpg&refer=http%3A%2F%2Finews.gtimg.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1645606804&t=01f36b17184f0a06a5350264233f166a",
    (texture: Texture) => {
      texture.wrapS = texture.wrapT = RepeatWrapping;
      var bottomAdGeo = new BoxGeometry(dw, 60, 8); // 柜子门宽，高，厚
      var bottomAd = new Mesh(bottomAdGeo, [
        new MeshLambertMaterial({ color: themeColor }),
        new MeshLambertMaterial({ color: themeColor }),
        new MeshLambertMaterial({ color: themeColor }),
        new MeshLambertMaterial({ color: themeColor }),
        new MeshLambertMaterial({ color: themeColor, map: texture }),
        new MeshLambertMaterial({ color: themeColor }),
      ]);
      bottomAd.name = "Top Ad";
      bottomAd.position.set(dw / 2, dh - 300, dw);
      frontGroup.add(bottomAd);
    }
  );

  refrigeratorGroup.add(frontGroup);

  // 机身
  var bodyGeo = new BoxGeometry(dw, dh, dd); // 柜子门宽，高，厚
  var bodyMaterials: MeshBasicMaterial[] = [
    new MeshLambertMaterial({ color: 0x666767 }),
    new MeshLambertMaterial({ color: 0x666767 }),
    new MeshLambertMaterial({ color: 0x666767 }),
    new MeshLambertMaterial({ color: 0x666767 }),
    new MeshLambertMaterial({ color: 0x666767 }),
    new MeshLambertMaterial({ color: 0x666767 }),
  ];
  var leftAdTexture: TextureLoader = new TextureLoader();
  leftAdTexture.load(
    "https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg.zcool.cn%2Fcommunity%2F01b7be568248006ac7251bb60e38ab.png%401280w_1l_2o_100sh.png&refer=http%3A%2F%2Fimg.zcool.cn&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1645607486&t=0b14abfa748321bbdd466be1b0183ae2",
    (texture: Texture) => {
      texture.wrapS = texture.wrapT = RepeatWrapping;
      bodyMaterials.splice(
        0,
        1,
        new MeshLambertMaterial({ color: 0x666767, map: texture })
      );
    }
  );
  var rightAdTexture: TextureLoader = new TextureLoader();
  rightAdTexture.load(
    "https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg.zcool.cn%2Fcommunity%2F01b7be568248006ac7251bb60e38ab.png%401280w_1l_2o_100sh.png&refer=http%3A%2F%2Fimg.zcool.cn&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1645607486&t=0b14abfa748321bbdd466be1b0183ae2",
    (texture: Texture) => {
      texture.wrapS = texture.wrapT = RepeatWrapping;
      bodyMaterials.splice(
        1,
        1,
        new MeshLambertMaterial({ color: 0x666767, map: texture })
      );
    }
  );
  var body = new Mesh(bodyGeo, bodyMaterials);
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
    }),
    new MeshLambertMaterial({
      color: 0xffffff,
    }),
    new MeshLambertMaterial({
      color: 0xffffff,
    }),
    new MeshLambertMaterial({
      color: 0xffffff,
    }),
    new MeshLambertMaterial({
      color: 0xffffff,
    }), // 玻璃质感
    new MeshLambertMaterial({
      color: 0xffffff,
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

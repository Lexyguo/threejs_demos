<script lang="ts">
import {
  Mesh,
  Scene,
  Group,
  Texture,
  BoxGeometry,
  TextureLoader,
  MeshBasicMaterial,
  MeshLambertMaterial,
} from "three";

export function init(
  x: number,
  y: number,
  z: number,
  scene: Scene,
  render: Function
) {
  var innnerX = x;
  var innnerY = y;
  var innnerZ = z;

  var dk = 50;
  var dc = 50;

  // 设置机箱的外壳
  var refrigeratorMatLambert: MeshLambertMaterial;
  var refrigeratorMatBasic: MeshBasicMaterial;
  var refrigeratorzMat: Mesh;

  // 正视图
  var frontTexture: TextureLoader = new TextureLoader();
  frontTexture.load("/src/assets/img/refrigerator_front.png");
  // 左视图
  var leftTexture: TextureLoader = new TextureLoader();
  leftTexture.load(
    "/src/assets/img/refrigerator_front.png",
    (texture: Texture) => {
      refrigeratorMatLambert = new MeshLambertMaterial({
        color: 0xeeeeee,
        map: texture,
      });
      refrigeratorMatBasic = new MeshBasicMaterial({
        color: 0xeeeeee,
        map: texture,
      });

      var refrigeratorGeo = new BoxGeometry(dk, 2, dc); //箱主体底宽30，高2，长40
      var refrigerator = new Mesh(refrigeratorGeo, refrigeratorMatBasic);
      refrigerator.position.set(0, 1, 0);

      var refrigeratorzGeo = new BoxGeometry(2, 88, dc); //箱左侧，厚2，高88，长40
      var refrigeratorzMaterials = [];
      refrigeratorzMaterials.push(
        //push顺序：X轴正、反，Y轴正、反，Z轴正、反
        refrigeratorMatLambert,
        refrigeratorMatLambert,
        refrigeratorMatLambert,
        refrigeratorMatLambert,
        new MeshBasicMaterial({
          color: 0xbebebe,
          map: texture2,
        }),
        refrigeratorMatBasic
      );
      var refrigeratorz = new Mesh(refrigeratorzGeo, refrigeratorzMaterials);
      refrigeratorz.position.set(dk / 2 - 1, 46, 0);

      var refrigeratoryGeo = new BoxGeometry(2, 88, dc); //箱左侧，厚2，高88，长40
      var refrigeratoryMaterials = [];
      refrigeratoryMaterials.push(
        refrigeratorMatLambert,
        refrigeratorMatBasic,
        refrigeratorMatLambert,
        refrigeratorMatLambert,
        new MeshBasicMaterial({
          color: 0xbebebe,
          map: texture3,
        }),
        refrigeratorMatBasic
      );
      var refrigeratory = new Mesh(refrigeratoryGeo, refrigeratoryMaterials);
      refrigeratory.position.set(-dk / 2 + 1, 46, 0);

      var refrigeratorhGeo = new BoxGeometry(dk - 4, 88, 2); //后板宽26，高88，厚2
      var refrigeratorh = new Mesh(refrigeratorhGeo, refrigeratorMatBasic);

      refrigeratorh.position.set(0, 46, 0 - dc / 2 + 1);

      var refrigeratorsGeo = new BoxGeometry(dk, 2, dc);
      var refrigeratorsMaterials = [];
      refrigeratorsMaterials.push(
        refrigeratorMatBasic,
        refrigeratorMatBasic,
        refrigeratorMatBasic,
        refrigeratorMatLambert,
        refrigeratorMatLambert,
        refrigeratorMatLambert
      );
      var refrigerators = new Mesh(refrigeratorsGeo, refrigeratorsMaterials);
      refrigerators.position.set(0, 91, 0);
      refrigerators.name = "refrigerators";

      refrigeratorGroup.add(
        refrigeratord,
        refrigeratorz,
        refrigeratory,
        refrigeratorh,
        refrigerators
      ); //refrigeratorGroup不包括机箱门

      //设置机箱门
      var menGroup = new Group();
      menGroup.position.set(innnerX + 15, innnerY, innnerZ + 20);
      //   menGroup.name = this.number; //机箱门的名字为输入的编号

      var menGeo = new BoxGeometry(dk, 92, 1); //机箱们宽，高，厚
      var mMaterials = [];
      mMaterials.push(
        new MeshLambertMaterial({ color: 0x999999 }),
        new MeshLambertMaterial({ color: 0x999999 }),
        new MeshLambertMaterial({ color: 0x999999 }),
        new MeshLambertMaterial({ color: 0x999999 }),
        new MeshLambertMaterial({
          map: ImageUtils.loadTexture("img/rack_front_door.jpg", {}, render),
          overdraw: true,
        }),
        new MeshBasicMaterial({
          map: ImageUtils.loadTexture("img/rack_door_back.jpg", {}, render),
          overdraw: true,
        })
      );

      var men = new Mesh(menGeo, mMaterials);
      men.name = "men";
      men.position.set(-dk / 2, 46, 0.5);
      menGroup.add(men);

      scene.add(refrigeratorGroup, menGroup);
    }
  );
  // 右视图
  var rightTexture: TextureLoader = new TextureLoader();
  rightTexture.load("/src/assets/img/refrigerator_front.png");
  // 背面
  var frontTexture: TextureLoader = new TextureLoader();
  frontTexture.load("/src/assets/img/refrigerator_front.png");

  var refrigeratorGroup = new Group();
  // boxGroup的平面中心是机柜主体的平面中心
  refrigeratorGroup.position.set(innnerX, innnerY, innnerZ);
  refrigeratorGroup.name = "refrigeratorGroup";
}
</script>

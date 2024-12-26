<template>
  <div id="app">
    <nav class="sidebar close">
      <header>
        <div class="image-text">
          <span class="image">
            <!-- <img src="./" alt=""> -->
          </span>
          <div class="text logo-text">
            <span class="name">F8VPS</span>
            <span class="profession">Web Developer</span>
          </div>
        </div>

        <i class="bx bx-chevron-right toggle"></i>
      </header>
      <div class="menu-bar">
        <div class="menu">
          <li class="search-box">
            <i class="bx bx-search icon"></i>
            <input type="text" placeholder="Search...">
          </li>

          <ul class="menu-links">

            <li class="nav-link" @click="groundedit()">
              <a href="#">
                <i class="bx bx-home-alt icon"></i>
                <span class="text nav-text">Ground</span>
              </a>
            </li>

            <!-- 下面的小li都是一样的样式，只需要修改文字和图标 -->
            <li class="nav-link">
              <a href="#">
                <i class='bx bx-bar-chart-alt-2 icon'></i>
                <span class="text nav-text">Revenue</span>
              </a>
            </li>

            <li class="nav-link">
              <a href="#">
                <i class='bx bx-bell icon'></i>
                <span class="text nav-text">Notifications</span>
              </a>
            </li>

            <li class="nav-link">
              <a href="#">
                <i class='bx bx-pie-chart-alt icon'></i>
                <span class="text nav-text">Analytics</span>
              </a>
            </li>

            <li class="nav-link">
              <a href="#">
                <i class='bx bx-heart icon'></i>
                <span class="text nav-text">Likes</span>
              </a>
            </li>

            <li class="nav-link">
              <a href="#">
                <i class='bx bx-wallet icon'></i>
                <span class="text nav-text">Wallets</span>
              </a>
            </li>
          </ul>
        </div>
        <div class="bottom-content">
          <li>
            <a href="#">
              <i class="bx bx-log-out icon"></i>
              <span class="text nav-text">Logout</span>
            </a>
          </li>
          <li class="mode">
            <div class="sun-moon">
              <i class="bx bx-moon icon moon"></i>
              <i class="bx bx-sun icon sun"></i>
            </div>
            <span class="mode-text text">Dark Mode</span>
            <div class="toggle-switch">
              <span class="switch"></span>
            </div>
          </li>
        </div>

      </div>
    </nav>

    <nav class="sidebar_right" v-show="GroundEdit">
      <header>
        <div class="image-text">
          <span class="image">
            <!-- <img src="./" alt=""> -->
          </span>
          <div class="text logo-text">
            <span class="name">商品定制</span>
          </div>
        </div>

      </header>
      <div class="menu-bar">
        <div class="menu">
          <ul class="menu-links">
            <li class="nav-link">
              <a href="#">
                <i class="bx bx-home-alt icon"></i>
                <span class="text nav-text">{{ ground_check }}</span>
              </a>
            </li>

            <li class="nav-link">
              <span class="bx">Width</span>
              <input v-model="width" class="text nav-text"></input>
            </li>

            <li class="nav-link">
              <span class="bx">Height</span>
              <input v-model="height" class="text nav-text"></input>
            </li>
            <!-- 材质 -->
            <li class="nav-link" @click="addmaterial()">
              <a href="#">
                <i class="bx bx-home-alt icon"></i>
                <span class="text nav-text">{{ material_check }}</span>
              </a>
            </li>

            <li class="nav-link" @click="logo_1()">
              <a href="#">
                <i class="bx bx-home-alt icon"></i>
                <span class="text nav-text">配件</span>
              </a>
            </li>

            <li class="nav-link">
              <ColorPicker v-model="color"></ColorPicker>
            </li>


          </ul>
        </div>

      </div>
    </nav>
  </div>

  <div class="home">
    <canvas id="show" touch-action="none"></canvas>

  </div>

</template>

<script setup>
import ColorPicker from '@mcistudio/vue-colorpicker'
import '@mcistudio/vue-colorpicker/dist/style.css'
</script>

<script>
import * as BABYLON from 'babylonjs'; //Babylon
//import earcut from 'earcut';
import * as BABYLON_LOADER from 'babylonjs-loaders';
//import * as GUI from 'babylonjs-gui';

// import _ from 'lodash'; //防抖

export default {

  data() {
    return {
      scene: null,
      GroundEdit: true,
      width: 22,
      height: 32,
      ground_check: '产品尺寸',
      material_check: '商品1',
      color: '#ff0000',
      newMaterial: null,
      newMaterial2: null,

      bag: null,
      // bag_2: null,
      // bag2_1: null,
      // bag2_2: null,
      // bag2_3: null,
      // bag_2_check: false,
    };
  },

  mounted() {
    const body = document.querySelector('body');
    const sidebar = body.querySelector('nav');
    const toggle = body.querySelector('.toggle');
    const searchBtn = body.querySelector('.search-box');
    const modeSwitch = body.querySelector('.toggle-switch');
    const modeText = body.querySelector('.mode-text');
    toggle.addEventListener('click', () => {
      sidebar.classList.toggle('close');
    });
    searchBtn.addEventListener('click', () => {
      sidebar.classList.remove('close');
    });
    modeSwitch.addEventListener('click', () => {
      body.classList.toggle('dark');
      if (body.classList.contains('dark')) {
        modeText.innerText = "Light mode";
      } else {
        modeText.innerText = "Dark mode";
      }
    });


    var canvas = document.getElementById("show"); // 创建画布
    var engine = new BABYLON.Engine(canvas, true); // 创建渲染引擎
    const scene = new BABYLON.Scene(engine);
    // const whiteColor = new BABYLON.Color3(1, 1, 1);
    // scene.clearColor = whiteColor;


    this.scene = scene;

    //导入模型
    BABYLON.SceneLoader.ImportMeshAsync("", "./", "11.glb").then((result) => {
      console.log(result)
      var bag = result.meshes[1]
      this.newMaterial = bag.material;
      this.bag = bag;
    });

    //横向尺子横线
    const rulerMesh = BABYLON.MeshBuilder.CreateLines("ruler", {
      points: [new BABYLON.Vector3(-0.2, 0, 0), new BABYLON.Vector3(1.2, 0, 0)]
    }, scene);
    rulerMesh.position.x = -0.5;
    rulerMesh.position.y = -0.25;
    //横向尺子长刻度
    const tickLength = 0.05;
    for (let i = 0.1; i < 1.6; i += 0.1) {
      const tickStart = new BABYLON.Vector3(i, 0, 0);
      const tickEnd = new BABYLON.Vector3(i, -tickLength, 0);
      const tick = BABYLON.MeshBuilder.CreateLines("tick" + i, {
        points: [tickStart, tickEnd]
      }, scene);
      tick.position.x = -0.8;
      tick.position.y = -0.2;
    }
    //横向尺子短刻度
    const tickLength_cm = 0.02;
    for (let i = 0.1; i < 1.5; i += 0.01) {
      const tickStart = new BABYLON.Vector3(i, 0, 0);
      const tickEnd = new BABYLON.Vector3(i, -tickLength_cm, 0);
      const tick_cm = BABYLON.MeshBuilder.CreateLines("tick" + i, {
        points: [tickStart, tickEnd]
      }, scene);
      tick_cm.position.x = -0.8;
      tick_cm.position.y = -0.23;
    }

    //尺子数字
    // const textMesh = BABYLON.MeshBuilder.CreateText("123", {
    //   depth: 0.2
    // }, scene);


    // 纵向尺子竖线
    const rulerMesh2 = BABYLON.MeshBuilder.CreateLines("ruler", {
      points: [new BABYLON.Vector3(0, -0.2, 0), new BABYLON.Vector3(0, 1.2, 0)] // 将x轴坐标改为y轴坐标，构建纵向的尺子主线
    }, scene);
    rulerMesh2.position.x = 0; // 调整横向位置，使其看起来更合适
    rulerMesh2.position.y = -1; // 调整纵向位置，将尺子整体下移

    // 纵向尺子长刻度
    const tickLength2 = 0.05;
    for (let i = 0.1; i < 1.6; i += 0.1) {
      const tickStart = new BABYLON.Vector3(0, i, 0); // 这里将刻度起始点的x坐标改为0，使用y坐标来表示刻度位置
      const tickEnd = new BABYLON.Vector3(-tickLength2, i, 0); // 刻度终点的x坐标为负，实现纵向向下延伸的刻度线
      const tick2 = BABYLON.MeshBuilder.CreateLines("tick" + i, {
        points: [tickStart, tickEnd]
      }, scene);
      tick2.position.x = -0.2; // 调整横向位置
      tick2.position.y = -0.8; // 调整纵向位置，将长刻度放置在合适位置
    }

    // 纵向尺子短刻度
    const tickLength2_cm = 0.02;
    for (let i = 0.1; i < 1.5; i += 0.01) {
      const tickStart = new BABYLON.Vector3(0, i, 0);
      const tickEnd = new BABYLON.Vector3(-tickLength2_cm, i, 0);
      const tick2_cm = BABYLON.MeshBuilder.CreateLines("tick" + i, {
        points: [tickStart, tickEnd]
      }, scene);
      tick2_cm.position.x = -0.23;
      tick2_cm.position.y = -0.8;
    }

    // const guiManager = new GUI.AdvancedDynamicTexture.CreateFullscreenUI("UI");
    // var zeroText = new GUI.TextBlock();
    // zeroText.text = "1 2 3 4 5 6 7 8 9";
    // zeroText.color = "white";
    // zeroText.fontSize = 24;
    // zeroText.left = "100px";
    // zeroText.top = "100px";
    // guiManager.addControl(zeroText);




    // const ambientLight = new BABYLON.AmbientLight("ambientLight", new BABYLON.Color3(0.2, 0.2, 0.2), this.scene);
    // ambientLight.enabled = true;

    // 创建从上面照射的平行光
    const topDirectionalLight = new BABYLON.PointLight("topLight", new BABYLON.Vector3(0, 1, 0), this.scene);
    topDirectionalLight.intensity = 1;
    topDirectionalLight.range = 10;

    // 创建从正面照射的平行光
    const frontLight = new BABYLON.PointLight("frontLight", new BABYLON.Vector3(0, 0, -1), this.scene);
    frontLight.intensity = 1;
    frontLight.range = 10;
    frontLight.position.x = 0.3;

    // 创建从背面照射的平行光
    const backLight = new BABYLON.PointLight("backLight", new BABYLON.Vector3(0, 0, 1), this.scene);
    backLight.intensity = 1; // 设置光强度，可根据实际效果调整
    backLight.range = 10;

    // 创建从左侧照射的平行光
    const leftLight = new BABYLON.PointLight("leftLight", new BABYLON.Vector3(-1, 0, 0), this.scene);
    leftLight.intensity = 1;
    leftLight.range = 10;

    // 创建从右侧照射的平行光
    const rightLight = new BABYLON.PointLight("rightLight", new BABYLON.Vector3(1, 0, 0), this.scene);
    rightLight.intensity = 1;
    rightLight.range = 10;

    //const box = BABYLON.MeshBuilder.CreateBox("box", { width: 0.2, height: 0.2, depth: 0.2 });

    //设置摄像头
    const camera = new BABYLON.ArcRotateCamera("camera", -1.6, 1.6, 150, new BABYLON.Vector3(0, 0, 0));
    camera.radius = 1.2;
    camera.setTarget(BABYLON.Vector3.Zero());
    camera.attachControl(canvas, true);
    camera.upperBetaLimit = Math.PI / 2.2;
    camera.lowerRadiusLimit = 1.2;
    camera.upperRadiusLimit = 1.2;


    engine.runRenderLoop(function () {
      scene.render(); // 渲染场景

    });

    window.addEventListener("resize", function () {
      engine.resize();
    });


  },
  methods: {
    //显示编辑栏
    groundedit() {
      console.log("执行了groundedit")
      this.GroundEdit = !this.GroundEdit
    },
    //添加材质
    logo_1() {
      console.log("执行了logo_1")
      //设置地面颜色
      if (this.material_check == '商品1') {
        this.material_check = '商品2'

      } else {
        this.material_check = '商品1'
      }
      this.bag.isVisible = !this.bag.isVisible;
    }
  },
  watch: {
    width(newValue, oldValue) {
      console.log("width:", this.width / 22)
      if (this.width > 10) {
        this.bag.scaling = new BABYLON.Vector3(this.width / 22, this.height / 32, 1);

      }
    },
    height(newValue, oldValue) {
      console.log("height", this.height / 32)
      if (this.height > 20) {
        this.bag.scaling = new BABYLON.Vector3(this.width / 22, this.height / 32, 1);
        // this.bag_2.position.y = 0.00611 * this.height - 0.03552;

      }
    },
    color(newValue, oldValue) {
      //setTimeout(() => {
      const color_r = newValue.color.r / 255;
      const color_g = newValue.color.g / 255;
      const color_b = newValue.color.b / 255;
      console.log('接收到的RGB的数值是:', color_r, color_g, color_b);

      // 其他PBR材质属性可以根据需要设置，如粗糙度、金属度等
      this.newMaterial.roughness = 0.5;
      this.newMaterial.metallic = 0.1;

      if (!color_r == 0) {
        this.bag.material.albedoColor = new BABYLON.Color3(color_r, color_g, color_b);
        console.log(this.bag.material.albedoColor);
      }
    },
  },

};
</script>


<style>
@import url('https://unpkg.com/boxicons@2.1.1/css/boxicons.min.css');

html,
body {
  overflow: hidden;
  width: 100%;
  height: 100%;
  margin: 0;
  padding: 0;
}

#show {
  width: 100%;
  height: 100%;
}



* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Poppins", sans-serif;
}

/* 一些需要重复使用的样式 */
:root {
  /* 颜色 */
  --body-color: #e4e9f7;
  --sidebar-color: #fff;
  --primary-color: #695cfe;
  --primary-color-light: #f6f5ff;
  --toggle-color: #ddd;
  --text-color: #707070;

  /* 过渡效果 */
  --tran-02: all 0.2s ease;
  --tran-03: all 0.3s ease;
}

body {
  min-height: 100vh;
  background-color: var(--body-color);
  transition: var(--tran-03);
}

::selection {
  background-color: var(--primary-color);
  color: #fff;
}

/* 当body标签拥有dark类名的时候的样式 */
body.dark {
  --body-color: #18191a;
  --sidebar-color: #242526;
  --primary-color: #3a3b3c;
  --primary-color-light: #3a3b3c;
  --toggle-color: #fff;
  --text-color: #ccc;
}

/* sidebar上的初始化样式 */
.sidebar {
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  width: 250px;
  padding: 10px 14px;
  background: var(--sidebar-color);
  transition: var(--tran-03);
  z-index: 100;
}

.sidebar.close {
  width: 88px;
}

.sidebar li {
  height: 50px;
  list-style: none;
  display: flex;
  align-items: center;
  margin-top: 10px;
}

.sidebar header .image,
.sidebar .icon {
  min-width: 60px;
  border-radius: 6px;
}

.sidebar .icon {
  min-width: 60px;
  border-radius: 6px;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 20px;
}

.sidebar .text,
.sidebar .icon {
  color: var(--text-color);
  transition: var(--tran-03);
}

.sidebar .text {
  font-size: 17px;
  font-weight: 500;
  white-space: nowrap;
  opacity: 1;
}

.sidebar.close .text {
  opacity: 0;

}

/* header部分样式 */

.sidebar header {
  position: relative;
}

.sidebar header .image-text {
  display: flex;
  align-items: center;
}

.sidebar header .logo-text {
  display: flex;
  flex-direction: column;
}

header .image-text .name {
  margin-top: 2px;
  font-size: 18px;
  font-weight: 600;
}

header .image-text .profession {
  font-size: 16px;
  margin-top: -2px;
  display: block;
}

.sidebar header .image {
  display: flex;
  align-items: center;
  justify-content: center;
}

.sidebar header .image img {
  width: 40px;
  border-radius: 6px;
}

.sidebar header .toggle {
  position: absolute;
  top: 50%;
  right: -25px;
  transform: translateY(-50%) rotate(180deg);
  height: 25px;
  width: 25px;
  background-color: var(--primary-color);
  color: var(--sidebar-color);
  display: flex;
  border-radius: 50%;
  align-items: center;
  justify-content: center;
  font-size: 22px;
  cursor: pointer;
  transition: var(--tran-03);
}

body.dark .sidebar header .toggle {
  color: var(--text-color);
}

.sidebar.close .toggle {
  transform: translateY(-50%) rotate(0deg);
}

.sidebar .menu {
  margin-top: 40px;
}

.sidebar li.search-box {
  border-radius: 6px;
  background-color: var(--primary-color-light);
  cursor: pointer;
  transition: var(--tran-03);
}

.sidebar li.search-box input {
  height: 100%;
  width: 100%;
  outline: none;
  border: none;
  background-color: var(--primary-color-light);
  color: var(--text-color);
  border-radius: 6px;
  font-size: 17px;
  font-weight: 500;
  transition: var(--tran-03);
}

.sidebar li a {
  list-style: none;
  height: 100%;
  background-color: transparent;
  display: flex;
  align-items: center;
  height: 100%;
  width: 100%;
  border-radius: 6px;
  text-decoration: none;
  transition: var(--tran-03);
}

.sidebar li a:hover {
  background-color: var(--primary-color);
}

.sidebar li a:hover .icon,
.sidebar li a:hover .text {
  color: var(--sidebar-color);
}

body.dark .sidebar li a:hover .icon,
body.dark .sidebar li a:hover .text {
  color: var(--text-color);
}

.sidebar .menu-bar {
  height: calc(100% - 55px);
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  overflow-y: scroll;
}

.menu-bar::-webkit-scrollbar {
  display: none;
}

.sidebar .menu-bar .mode {
  border-radius: 6px;
  background-color: var(--primary-color-light);
  position: relative;
  transition-timing-function: var(--tran-03);
}

.menu-bar .mode .sun-moon {
  height: 50px;
  width: 60px;
}

.mode .sun-moon i {
  position: absolute;
}

.mode .sun-moon i.sun {
  opacity: 0;
}

body.dark .mode .sun-moon i.sun {
  opacity: 1;
}

body.dark .mode .sun-moon i.moon {
  opacity: 0;
}

.menu-bar .bottom-content .toggle-switch {
  position: absolute;
  right: 0;
  height: 100%;
  min-width: 60px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 6px;
  cursor: pointer;
}

.toggle-switch .switch {
  position: relative;
  height: 22px;
  width: 40px;
  border-radius: 25px;
  background-color: var(--toggle-color);
  transition: var(--tran-03);
}

.switch::before {
  content: "";
  position: absolute;
  width: 15px;
  height: 15px;
  border-radius: 50%;
  top: 50%;
  left: 5px;
  transform: translateY(-50%);
  background-color: var(--sidebar-color);
  transition: var(--tran-03);
}

body.dark .switch::before {
  left: 20px;
}

.home {
  position: absolute;
  top: 0;
  left: 250px;
  height: 100vh;
  width: calc(100% - 250px);
  background-color: var(--body-color);
  transition: var(--tran-03);
}

.home .text {
  font-size: 30px;
  font-weight: 500;
  color: var(--text-color);
  padding: 12px 60px;
}

.sidebar.close~.home {
  left: 78px;
  height: 100vh;
  width: calc(100% - 78px);
}

body.dark .home .text {
  color: var(--text-color);
}

/* 字体库不一定要是我这个，甚至不用也可以 */
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Poppins", sans-serif;
}

/* 一些需要重复使用的样式 */
:root {
  /* 颜色 */
  --body-color: #e4e9f7;
  --sidebar_right-color: #fff;
  --primary-color: #695cfe;
  --primary-color-light: #f6f5ff;
  --toggle-color: #ddd;
  --text-color: #707070;

  /* 过渡效果 */
  --tran-02: all 0.2s ease;
  --tran-03: all 0.3s ease;
}

body {
  min-height: 100vh;
  background-color: var(--body-color);
  transition: var(--tran-03);
}

::selection {
  background-color: var(--primary-color);
  color: #fff;
}

/* 当body标签拥有dark类名的时候的样式 */
body.dark {
  --body-color: #18191a;
  --sidebar_right-color: #242526;
  --primary-color: #3a3b3c;
  --primary-color-light: #3a3b3c;
  --toggle-color: #fff;
  --text-color: #ccc;
}

/* sidebar_right上的初始化样式 */
.sidebar_right {
  position: absolute;
  top: 0;
  right: 0;
  height: 100%;
  width: 250px;
  padding: 10px 14px;
  background: var(--sidebar_right-color);
  transition: var(--tran-03);
  z-index: 100;
}

.sidebar_right.close {
  width: 300px;
}

.sidebar_right li {
  height: 50px;
  list-style: none;
  display: flex;
  align-items: center;
  margin-top: 10px;
}

.sidebar_right header .image,
.sidebar_right .icon {
  min-width: 60px;
  border-radius: 6px;
}

.sidebar_right .icon {
  min-width: 60px;
  border-radius: 6px;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 20px;
}

.sidebar_right .text,
.sidebar_right .icon {
  color: var(--text-color);
  transition: var(--tran-03);
}

.sidebar_right .text {
  font-size: 17px;
  font-weight: 500;
  white-space: nowrap;
  opacity: 1;
}

.sidebar_right.close .text {
  opacity: 0;

}

/* header部分样式 */

.sidebar_right header {
  position: relative;
}

.sidebar_right header .image-text {
  display: flex;
  align-items: center;
}

.sidebar_right header .logo-text {
  display: flex;
  flex-direction: column;
}

header .image-text .name {
  margin-top: 2px;
  font-size: 18px;
  font-weight: 600;
}

header .image-text .profession {
  font-size: 16px;
  margin-top: -2px;
  display: block;
}

.sidebar_right header .image {
  display: flex;
  align-items: center;
  justify-content: center;
}

.sidebar_right header .image img {
  width: 40px;
  border-radius: 6px;
}

.sidebar_right header .toggle {
  position: absolute;
  top: 50%;
  right: -25px;
  transform: translateY(-50%) rotate(180deg);
  height: 25px;
  width: 25px;
  background-color: var(--primary-color);
  color: var(--sidebar_right-color);
  display: flex;
  border-radius: 50%;
  align-items: center;
  justify-content: center;
  font-size: 22px;
  cursor: pointer;
  transition: var(--tran-03);
}

body.dark .sidebar_right header .toggle {
  color: var(--text-color);
}

.sidebar_right.close .toggle {
  transform: translateY(-50%) rotate(0deg);
}

.sidebar_right .menu {
  margin-top: 40px;
}

.sidebar_right li.search-box {
  border-radius: 6px;
  background-color: var(--primary-color-light);
  cursor: pointer;
  transition: var(--tran-03);
}

.sidebar_right li.search-box input {
  height: 100%;
  width: 100%;
  outline: none;
  border: none;
  background-color: var(--primary-color-light);
  color: var(--text-color);
  border-radius: 6px;
  font-size: 17px;
  font-weight: 500;
  transition: var(--tran-03);
}

.sidebar_right li a {
  list-style: none;
  height: 100%;
  background-color: transparent;
  display: flex;
  align-items: center;
  height: 100%;
  width: 100%;
  border-radius: 6px;
  text-decoration: none;
  transition: var(--tran-03);
}

.sidebar_right li a:hover {
  background-color: var(--primary-color);
}

.sidebar_right li a:hover .icon,
.sidebar_right li a:hover .text {
  color: var(--sidebar_right-color);
}

body.dark .sidebar_right li a:hover .icon,
body.dark .sidebar_right li a:hover .text {
  color: var(--text-color);
}

.sidebar_right .menu-bar {
  height: calc(100% - 55px);
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  overflow-y: scroll;
}

.menu-bar::-webkit-scrollbar {
  display: none;
}

.sidebar_right .menu-bar .mode {
  border-radius: 6px;
  background-color: var(--primary-color-light);
  position: relative;
  transition-timing-function: var(--tran-03);
}

.menu-bar .mode .sun-moon {
  height: 50px;
  width: 60px;
}

.mode .sun-moon i {
  position: absolute;
}

.mode .sun-moon i.sun {
  opacity: 0;
}

body.dark .mode .sun-moon i.sun {
  opacity: 1;
}

body.dark .mode .sun-moon i.moon {
  opacity: 0;
}

.menu-bar .bottom-content .toggle-switch {
  position: absolute;
  right: 0;
  height: 100%;
  min-width: 60px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 6px;
  cursor: pointer;
}

.toggle-switch .switch {
  position: relative;
  height: 22px;
  width: 40px;
  border-radius: 25px;
  background-color: var(--toggle-color);
  transition: var(--tran-03);
}

.switch::before {
  content: "";
  position: absolute;
  width: 15px;
  height: 15px;
  border-radius: 50%;
  top: 50%;
  left: 5px;
  transform: translateY(-50%);
  background-color: var(--sidebar_right-color);
  transition: var(--tran-03);
}

body.dark .switch::before {
  left: 20px;
}

.home {
  position: absolute;
  top: 0;
  left: 55px;
  height: 100vh;
  width: 100%;
  background-color: var(--body-color);
  transition: var(--tran-03);
}

.home .text {
  font-size: 30px;
  font-weight: 500;
  color: var(--text-color);
  padding: 12px 60px;
}

.sidebar_right.close~.home {
  left: 78px;
  height: 100vh;
  width: calc(100% - 78px);
}

body.dark .home .text {
  color: var(--text-color);
}
</style>

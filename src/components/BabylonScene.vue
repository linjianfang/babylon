<script>
import * as BABYLON from 'babylonjs';
export default {
  name: 'BabylonScene',
  mounted() {
    // 在这里初始化Babylon.js场景
    this.initScene();
  },
  methods: {
    initScene() {
      // 创建一个canvas元素作为渲染目标
      const canvas = document.createElement('canvas');
      canvas.id = 'babylonCanvas';
      document.body.appendChild(canvas);
      // 创建一个Babylon.js引擎
      const engine = new BABYLON.Engine(canvas, true);
      // 创建一个场景
      const scene = new BABYLON.Scene(engine);
      // 创建一个相机
      const camera = new BABYLON.ArcRotateCamera('camera', -Math.PI / 2, Math.PI / 2.5, 3, BABYLON.Vector3.Zero(), scene);
      camera.attachControl(canvas, true);
      // 创建一个光源
      const light = new BABYLON.HemisphericalLight('light', new BABYLON.Vector3(0, 1, 0), scene);
      // 创建一个简单的立方体
      const box = BABYLON.MeshBuilder.CreateBox('box', { size: 1 }, scene);
      // 开始渲染循环
      engine.runRenderLoop(() => {
        scene.render();
      });
      // 处理窗口大小变化
      window.addEventListener('resize', () => {
        engine.resize();
      });
    }
  }
}
</script>
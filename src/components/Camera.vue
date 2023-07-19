<template>
  <canvas id="demo"></canvas>
</template>

<script>
import * as THREE from 'three';
import WebGL from '@/utils/WebGL';
import {OrbitControls} from 'three/examples/jsm/controls/OrbitControls.js';
import {GUI} from 'three/examples/jsm/libs/lil-gui.module.min.js';
import checker from '/src/assets/resources/images/checker.png'
import {RectAreaLightUniformsLib} from 'three/examples/jsm/lights/RectAreaLightUniformsLib.js';
import {RectAreaLightHelper} from 'three/examples/jsm/helpers/RectAreaLightHelper.js';
class ColorGUIHelper {
  constructor(object, prop) {
    this.object = object;
    this.prop = prop;
  }
  get value() {
    return `#${this.object[this.prop].getHexString()}`;
  }
  set value(hexString) {
    this.object[this.prop].set(hexString);
  }
}
class DegRadHelper {
  constructor(obj, prop) {
    this.obj = obj;
    this.prop = prop;
  }
  get value() {
    return THREE.MathUtils.radToDeg(this.obj[this.prop]);
  }
  set value(v) {
    this.obj[this.prop] = THREE.MathUtils.degToRad(v);
  }
}

export default {
  name: 'CameraComponent',
  data() {
    return {};
  },
  methods: {
    drawLight() {
      const canvas = document.querySelector('#demo');
      const scene = new THREE.Scene();
      const fov = 45;
      const aspect = 2; // the canvas default
      const near = 0.1;
      const far = 100;

      const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
      camera.position.set(0, 10, 20);

      const controls = new OrbitControls(camera, canvas);
      controls.target.set(0, 5, 0);
      controls.update();

      // 下面我们创建一些东西来打光。首先，创建一个地平面
      const planeSize = 40;

      const loader = new THREE.TextureLoader();
      const texture = loader.load(checker);
      texture.wrapS = THREE.RepeatWrapping;
      texture.wrapT = THREE.RepeatWrapping;
      texture.magFilter = THREE.NearestFilter;
      const repeats = planeSize / 2;
      texture.repeat.set(repeats, repeats);

      const planeGeo = new THREE.PlaneGeometry(planeSize, planeSize);
      const planeMat = new THREE.MeshStandardMaterial({
        map: texture,
        side: THREE.DoubleSide,
      });
      const mesh = new THREE.Mesh(planeGeo, planeMat);
      mesh.rotation.x = Math.PI * -.5;
      scene.add(mesh);

      // 接着再添加一个立方体和一个球体，这样我们就有三个物体可以打光。
      {
        const cubeSize = 4;
        const cubeGeo = new THREE.BoxGeometry(cubeSize, cubeSize, cubeSize);
        const cubeMat = new THREE.MeshStandardMaterial({color: '#8AC'});
        const mesh = new THREE.Mesh(cubeGeo, cubeMat);
        mesh.position.set(cubeSize + 1, cubeSize / 2, 0);
        scene.add(mesh);
      }
      {
        const sphereRadius = 3;
        const sphereWidthDivisions = 32;
        const sphereHeightDivisions = 16;
        const sphereGeo = new THREE.SphereGeometry(sphereRadius, sphereWidthDivisions, sphereHeightDivisions);
        const sphereMat = new THREE.MeshStandardMaterial({color: '#CA8'});
        const mesh = new THREE.Mesh(sphereGeo, sphereMat);
        mesh.position.set(-sphereRadius - 1, sphereRadius + 2, 0);
        scene.add(mesh);
      }
      
      // 环境光（AmbientLight） 首先创建一个 AmbientLight
      {
        // const color = 0xFFFFFF;
        // const intensity = 1;
        // const light = new THREE.AmbientLight(color, intensity);
        // scene.add(light);
  
        // const gui = new GUI();
        // gui.addColor(new ColorGUIHelper(light, 'color'), 'value').name('color');
        // gui.add(light, 'intensity', 0, 2, 0.01);
      }
      // 半球光（HemisphereLight）
      // 半球光（HemisphereLight）的颜色是从天空到地面两个颜色之间的渐变，与物体材质的颜色作叠加后得到最终的颜色效果。一个点受到的光照颜色是由所在平面的朝向（法向量）决定的 —— 面向正上方就受到天空的光照颜色，面向正下方就受到地面的光照颜色，其他角度则是两个颜色渐变区间的颜色。
      {
        // const skyColor = 0xB1E1FF;  // light blue
        // const groundColor = 0xB97A20;  // brownish orange
        // const intensity = 1;
        // const light = new THREE.HemisphereLight(skyColor, groundColor, intensity);
        // scene.add(light);
        // const gui = new GUI();
        // gui.addColor(new ColorGUIHelper(light, 'color'), 'value').name('skyColor');
        // gui.addColor(new ColorGUIHelper(light, 'groundColor'), 'value').name('groundColor');
        // gui.add(light, 'intensity', 0, 2, 0.01);
      }

      // 方向光（DirectionalLight）
      //  方向光（DirectionalLight）常常用来表现太阳光照的效果。
      {
        // const color = 0xFFFFFF;
        // const intensity = 1;
        // const light = new THREE.DirectionalLight(color, intensity);
        // light.position.set(0, 10, 0);
        // light.target.position.set(-5, 0, 0);
        // scene.add(light);
        // scene.add(light.target);
        

        // const helper = new THREE.DirectionalLightHelper(light);
        // scene.add(helper);
        // function makeXYZGUI(gui, vector3, name, onChangeFn) {
        //   const folder = gui.addFolder(name);
        //   folder.add(vector3, 'x', -10, 10).onChange(onChangeFn);
        //   folder.add(vector3, 'y', 0, 10).onChange(onChangeFn);
        //   folder.add(vector3, 'z', -10, 10).onChange(onChangeFn);
        //   folder.open();
        // }
        // function updateLight() {
        //   light.target.updateMatrixWorld();
        //   helper.update();
        // }
        // updateLight();

        // const gui = new GUI();
        // gui.addColor(new ColorGUIHelper(light, 'color'), 'value').name('color');
        // gui.add(light, 'intensity', 0, 2, 0.01);
        // makeXYZGUI(gui, light.position, 'position', updateLight);
        // makeXYZGUI(gui, light.target.position, 'target', updateLight);
      }

      // 点光源（PointLight）
      // 点光源（PointLight）表示的是从一个点朝各个方向发射出光线的一种光照效果
      {
        // const color = 0xFFFFFF;
        // const intensity = 1;
        // const light = new THREE.PointLight(color, intensity);
        // light.power = 800;
        // light.decay = 2;
        // light.distance = Infinity;
        // light.position.set(0, 10, 0);
        // scene.add(light);
        

        // const helper = new THREE.PointLightHelper(light);
        // scene.add(helper);
        // function makeXYZGUI(gui, vector3, name, onChangeFn) {
        //   const folder = gui.addFolder(name);
        //   folder.add(vector3, 'x', -10, 10).onChange(onChangeFn);
        //   folder.add(vector3, 'y', 0, 10).onChange(onChangeFn);
        //   folder.add(vector3, 'z', -10, 10).onChange(onChangeFn);
        //   folder.open();
        // }
        // function updateLight() {
        //   helper.update();
        // }

        // const gui = new GUI();
        // gui.addColor(new ColorGUIHelper(light, 'color'), 'value').name('color');
        // gui.add(light, 'intensity', 0, 2, 0.01);
        // gui.add(light, 'distance', 0, 40).onChange(updateLight);
        // gui.add(light, 'decay', 0, 4, 0.01);
        // gui.add(light, 'power', 0, 2000);
        // makeXYZGUI(gui, light.position, 'position', updateLight);
      }

      // 聚光灯（SpotLight）
      // 聚光灯可以看成是一个点光源被一个圆锥体限制住了光照的范围。实际上有两个圆锥，内圆锥和外圆锥。光照强度在两个锥体之间从设定的强度递减到 0
      // 聚光灯（SpotLight）类似方向光（DirectionalLight）一样需要一个目标点，光源的位置是圆锥的顶点，目标点处于圆锥的中轴线上。
      {
        // const color = 0xFFFFFF;
        // const intensity = 1;
        // const light = new THREE.SpotLight(color, intensity);
        // light.position.set(0, 10, 0);
        // light.target.position.set(-5, 0, 0);
        // scene.add(light);
        // scene.add(light.target);
        

        // const helper = new THREE.SpotLightHelper(light);
        // scene.add(helper);
        // function makeXYZGUI(gui, vector3, name, onChangeFn) {
        //   const folder = gui.addFolder(name);
        //   folder.add(vector3, 'x', -10, 10).onChange(onChangeFn);
        //   folder.add(vector3, 'y', 0, 10).onChange(onChangeFn);
        //   folder.add(vector3, 'z', -10, 10).onChange(onChangeFn);
        //   folder.open();
        // }
        // function updateLight() {
        //   light.target.updateMatrixWorld();
        //   helper.update();
        // }
        // updateLight();

        // const gui = new GUI();
        // gui.addColor(new ColorGUIHelper(light, 'color'), 'value').name('color');
        // gui.add(light, 'intensity', 0, 2, 0.01);
        // gui.add(light, 'distance', 0, 40).onChange(updateLight);
        // gui.add(new DegRadHelper(light, 'angle'), 'value', 0, 90).name('angle').onChange(updateLight);
        // gui.add(light, 'penumbra', 0, 1, 0.01);
        // makeXYZGUI(gui, light.position, 'position', updateLight);
        // makeXYZGUI(gui, light.target.position, 'target', updateLight);
      }

      // 矩形区域光（RectAreaLight）
      // 矩形区域光（RectAreaLight）, 顾名思义，表示一个矩形区域的发射出来的光照，例如长条的日光灯或者天花板上磨砂玻璃透进来的自然光。
      // RectAreaLight 只能影响 MeshStandardMaterial 和 MeshPhysicalMaterial，所以我们把所有的材质都改为 MeshStandardMaterial。
      {
        RectAreaLightUniformsLib.init();

        const color = 0xFFFFFF;
        const intensity = 5;
        const width = 12;
        const height = 4;
        const light = new THREE.RectAreaLight(color, intensity, width, height);
        light.position.set(0, 10, 0);
        light.rotation.x = THREE.MathUtils.degToRad(-90);
        scene.add(light);
        

        const helper = new RectAreaLightHelper(light);
        light.add(helper);

        function makeXYZGUI(gui, vector3, name, onChangeFn) {
          const folder = gui.addFolder(name);
          folder.add(vector3, 'x', -10, 10).onChange(onChangeFn);
          folder.add(vector3, 'y', 0, 10).onChange(onChangeFn);
          folder.add(vector3, 'z', -10, 10).onChange(onChangeFn);
          folder.open();
        }

        const gui = new GUI();
        gui.addColor(new ColorGUIHelper(light, 'color'), 'value').name('color');
        gui.add(light, 'intensity', 0, 10, 0.01);

        gui.add(light, 'width', 0, 20);
        gui.add(light, 'height', 0, 20);
        gui.add(new DegRadHelper(light.rotation, 'x'), 'value', -180, 180).name('x rotation');
        gui.add(new DegRadHelper(light.rotation, 'y'), 'value', -180, 180).name('y rotation');
        gui.add(new DegRadHelper(light.rotation, 'z'), 'value', -180, 180).name('z rotation');
        makeXYZGUI(gui, light.position, 'position');
      }


      const renderer = new THREE.WebGLRenderer({ antialias: true, canvas });
      renderer.physicallyCorrectLights = true;

      function resizeRendererToDisplaySize(renderer) {
        const canvas = renderer.domElement;
        const width = canvas.clientWidth;
        const height = canvas.clientHeight;
        const needResize = canvas.width !== width || canvas.height !== height;
        if (needResize) {
          renderer.setSize(width, height, false);
        }
        return needResize;
      }

      function animate(time) {
        time *= 0.001; // 将时间单位变为秒

        if (resizeRendererToDisplaySize(renderer)) {
          const canvas = renderer.domElement;
          camera.aspect = canvas.clientWidth / canvas.clientHeight;
          camera.updateProjectionMatrix();
        }

        renderer.render(scene, camera);
        requestAnimationFrame(animate);
      }
      if (WebGL.isWebGLAvailable()) {
        // Initiate function or other initializations here
        animate();
      } else {
        const warning = WebGL.getWebGLErrorMessage();
        document.body.appendChild(warning);
      }
    },
  },
  mounted() {
    this.drawLight();
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
h1,
h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

#demo {
  width: 100%;
  height: 100%;
  display: block;
}
html, body {
  margin: 0;
  height: 100%;
}
</style>

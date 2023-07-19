<template>
  <canvas id="demo"></canvas>
</template>

<script>
import * as THREE from 'three';
import { FontLoader } from 'three/examples/jsm/loaders/FontLoader';
import { ParametricGeometry } from 'three/examples/jsm/geometries/ParametricGeometry';
import { TextGeometry } from 'three/examples/jsm/geometries/TextGeometry';
import {GUI} from 'three/examples/jsm/libs/lil-gui.module.min.js';

import WebGL from '@/utils/WebGL';

export default {
  name: 'ScenceGraph',
  data() {
    return {};
  },
  methods: {
    createScene() {
      const canvas = document.querySelector('#demo');
      const scene = new THREE.Scene();
      const renderer = new THREE.WebGLRenderer({ antialias: true, canvas });
      const gui = new GUI();
      
      // 要更新旋转角度的对象数组
      const objects = [];

      const solarSystem = new THREE.Object3D();
      scene.add(solarSystem);
      objects.push(solarSystem);

      // 一球多用
      const radius = 1;
      const widthSegments = 6;
      const heightSegments = 6;
      const sphereGeometry = new THREE.SphereGeometry(
        radius,
        widthSegments,
        heightSegments
      );
      
      const sunMaterial = new THREE.MeshPhongMaterial({ emissive: 0xffff00 });
      const sunMesh = new THREE.Mesh(sphereGeometry, sunMaterial);
      sunMesh.scale.set(5, 5, 5); // 扩大太阳的大小
      solarSystem.add(sunMesh);
      objects.push(sunMesh);

      const earthOrbit = new THREE.Object3D();
      earthOrbit.position.x = 10;
      solarSystem.add(earthOrbit);
      objects.push(earthOrbit);

      const earthMaterial = new THREE.MeshPhongMaterial({
        color: 0x2233ff,
        emissive: 0x112244,
      });
      const earthMesh = new THREE.Mesh(sphereGeometry, earthMaterial);
      earthOrbit.add(earthMesh);
      objects.push(earthMesh);

      const moonOrbit = new THREE.Object3D();
      moonOrbit.position.x = 2;
      earthOrbit.add(moonOrbit);

      const moonMaterial = new THREE.MeshPhongMaterial({color: 0x888888, emissive: 0x222222});
      const moonMesh = new THREE.Mesh(sphereGeometry, moonMaterial);
      moonMesh.scale.set(.5, .5, .5);
      moonOrbit.add(moonMesh);
      objects.push(moonMesh);

      // 打开/关闭轴和网格的可见性
      // lil-gui 要求一个返回类型为bool型的属性
      // 来创建一个复选框，所以我们为 `visible`属性
      // 绑定了一个setter 和 getter。 从而让lil-gui
      // 去操作该属性.
      class AxisGridHelper {
        constructor(node, units = 10) {
          const axes = new THREE.AxesHelper();
          axes.material.depthTest = false;
          axes.renderOrder = 2; // 在网格渲染之后再渲染
          node.add(axes);
      
          const grid = new THREE.GridHelper(units, units);
          grid.material.depthTest = false;
          grid.renderOrder = 1;
          node.add(grid);
      
          this.grid = grid;
          this.axes = axes;
          this.visible = false;
        }
        get visible() {
          return this._visible;
        }
        set visible(v) {
          this._visible = v;
          this.grid.visible = v;
          this.axes.visible = v;
        }
      }

      function makeAxisGrid(node, label, units) {
        const helper = new AxisGridHelper(node, units);
        gui.add(helper, 'visible').name(label);
      }
      
      makeAxisGrid(solarSystem, 'solarSystem', 25);
      makeAxisGrid(sunMesh, 'sunMesh');
      makeAxisGrid(earthOrbit, 'earthOrbit');
      makeAxisGrid(earthMesh, 'earthMesh');
      makeAxisGrid(moonOrbit, 'moonOrbit');
      makeAxisGrid(moonMesh, 'moonMesh');
      gui.add(earthOrbit.position, 'x').name('earthOrbit-x');
      // 中心设置光源
      const color = 0xffffff;
      const intensity = 3;
      const light = new THREE.PointLight(color, intensity);
      scene.add(light);

      const fov = 40;
      const aspect = 2; // the canvas default
      const near = 0.1;
      const far = 1000;

      const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
      camera.position.set(0, 50, 0);
      camera.up.set(0, 0, 1);
      camera.lookAt(0, 0, 0);

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

        objects.forEach((obj) => {
          obj.rotation.y = time;
        });
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
    this.createScene()
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

<template>
  <div ref="threeContainer"></div>
</template>

<script setup lang="ts">
import * as THREE from 'three';
import { onMounted, ref } from 'vue';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';

const threeContainer = ref();

function threeJs() {
  // 创建场景
  const scene = new THREE.Scene();
  scene.background = new THREE.Color(0xbfe3dd);

  // 相机
  const camera = new THREE.PerspectiveCamera(40, window.innerWidth / window.innerHeight, 1, 100);
  // 相机位置 
  camera.position.set(3, 2, 10);

  const renderer = new THREE.WebGLRenderer({ antialias: true });  //抗锯齿
  // 设置渲染画布大小
  renderer.setSize(window.innerWidth, window.innerHeight);
  // 将渲染画布插入到body中
  threeContainer.value.appendChild(renderer.domElement); //renderer.domElement为渲染画布元素

  const controls = new OrbitControls(camera, renderer.domElement);
  controls.enableDamping = true; // 添加阻尼效果（惯性）
  controls.dampingFactor = 0.05; // 阻尼效果强度  
  controls.enableZoom = true; // 允许缩放  
  controls.zoomSpeed = 1.0; // 缩放速度  
  controls.target.set(0, 1, 0);

  function animate() {
      requestAnimationFrame(animate)
      renderer.render(scene, camera);
      controls.update();
  }

  const loader = new GLTFLoader();
  loader.load('src/assets/material/scene.gltf', 
    function (gltf: any) {
        const model = gltf.scene;
        model.position.set(1, 1, 0);
        model.scale.set(0.01, 0.01, 0.01);
        scene.add(model);
        animate();
    }, undefined, function (err: any) {
      console.error(err);
    }
  );

  const light = new THREE.AmbientLight(0xffffff);
  scene.add(light);
}

onMounted(() => {
  threeJs();
})

</script>
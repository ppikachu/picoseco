<script setup>
  import { ref, onMounted, onBeforeMount } from "vue"
  import { Clock, ACESFilmicToneMapping, Vector2, Vector3, sRGBEncoding, EquirectangularReflectionMapping, UVMapping, MeshStandardMaterial, MeshPhysicalMaterial, TextureLoader, RepeatWrapping, UniformsUtils, ShaderMaterial, Fog, FogExp2 } from "three"
  import { RGBELoader } from "three/examples/jsm/loaders/RGBELoader"
  import { SubsurfaceScatteringShader } from "three/examples/jsm/shaders/SubsurfaceScatteringShader.js"
  import {
    Camera,
    GltfModel,
    PointLight,
    Renderer,
    Scene,
    StandardMaterial,
    EffectPass,
  } from 'troisjs'
  import Title from '../src/components/Title.vue'

  var light,
  scene,
  gltfScene,
  camera,
  orbitCtrl,
  renderer

var clock = new Clock()

const rendererRef = ref()
const sceneRef = ref()
const cameraRef = ref()
const gltfRef = ref()

  onBeforeMount(() => {
    console.clear()
  })

  onMounted(() => {
    renderer = rendererRef.value.renderer
    scene = sceneRef.value.scene
    renderer.outputEncoding = sRGBEncoding
    renderer.toneMapping = ACESFilmicToneMapping
    renderer.toneMappingExposure = 1
    // turn on the physically correct lighting model
    renderer.physicallyCorrectLights = true
  })

  function onReady(gltf) {
  gltfScene = gltf.scene
  const exprimidor = gltfScene.getObjectByName('posta_m')

  // car paint
  const map = new TextureLoader().load( '/exp_baseColor.png' )
  map.encoding = sRGBEncoding
  const material = new MeshPhysicalMaterial({
    clearcoat: 1,
    clearcoatRoughness: 0.2,
    metalness: 0.4,
    roughness: 0.8,
    color: 0xffffff,
    map: map,
  })
  // self shadowing
  // exprimidor.receiveShadow = true
  // exprimidor.castShadow = true

  // environment
  const hdrimgUrl = '/Studio_80s.hdr'
  const scene = sceneRef.value.scene
  scene.fog = new Fog( 0xddeeee, 0.45, 0.8 )
  const loadedTexture = new RGBELoader().load(hdrimgUrl, () => {
    const envMap = loadedTexture
    scene.environment = envMap
    scene.environment.mapping = EquirectangularReflectionMapping
    exprimidor.material.envMap = envMap
    exprimidor.material.needsUpdate = true
  })

  exprimidor.material = material

  animate()
}

function animate() {
  requestAnimationFrame(animate)
  // rotate scene
  gltfScene.rotation.y = Math.PI * Math.sin(clock.getElapsedTime() / 5)
}
</script>

<template>
  <div class="h-screen flex items-center" >
     <Title />

    <Renderer ref="rendererRef" antialias shadow resize pointer :orbit-ctrl="{ enableDamping: true, dampingFactor: 0.05 }">
      <Camera ref="cameraRef" :position="{ x: -0.4, y: 0.4, z: 0.3 }" />
      <Scene ref="sceneRef" background="#dee">
        <PointLight helper ref="light1" color="#fff" :intensity="2" cast-shadow :shadow-map-size="{ width:2048, height:2048 }" :position="{ x: 1, y: 1, z: -5 }" />

        <GltfModel
          ref="gltfRef"
          src="/exp.gltf"
          :rotation="{ x: Math.PI/8, y: 0, z: 0 }"
          :position="{ x: -0.1, y: -0.1, z: 0 }"
          @load="onReady"
        />

      </Scene>
    </Renderer>
  </div>
</template>
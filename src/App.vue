<script setup lang="ts">
import { ref, onMounted, onBeforeMount } from "vue"
import { Clock, ACESFilmicToneMapping, sRGBEncoding, EquirectangularReflectionMapping, MeshPhysicalMaterial, TextureLoader, Fog } from "three"
import { RGBELoader } from "three/examples/jsm/loaders/RGBELoader"
import {
  Camera,
  GltfModel,
  PointLight,
  Renderer,
  Scene,
} from 'troisjs'
import Logo from '../src/components/Logo.vue'

var gltfScene, renderer, scene
var clock = new Clock()

const rendererRef = ref()
const sceneRef = ref()
const cameraRef = ref()
const gltfRef = ref()
const loadedModels = ref('opacity-100')

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
  const material = new MeshPhysicalMaterial({
    clearcoat: 1,
    clearcoatRoughness: 0.2,
    metalness: 0.4,
    roughness: 0.8,
    color: 0xffffff,
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
    //fade
    loadedModels.value = 'opacity-0'
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
     <Logo />

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
    
    <!--fadeScene-->
    <div :class="loadedModels" class="transition-opacity duration-200 absolute top-0 w-full h-screen flex justify-center items-center bg-[#ddeeee]" >
      <div class="animate-pulse flex space-x-4 items-center">
        <svg width="158" height="18" viewBox="0 0 158 18" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path fill-rule="evenodd" clip-rule="evenodd" d="M4 2H2V16H6V12H10V10H12V4H10V2H6H4ZM8 10V4H6V10H8ZM14 2H18V16H14V2ZM22 4H20V14H22V16H30V14H24V4H30V2H22V4ZM42 2H34V4H32V14H34V16H42V14H44V4H42V2ZM40 4H36V14H40V4ZM48 2H54V4H50V8H52V10H54V14H52V16H46V14H50V10H48V8H46V4H48V2ZM58 16H56V2H58H60H66V4H60V6H64V8H60V14H66V16H60H58ZM72 14H78V16H70V14H68V4H70V2H78V4H72V14ZM88 14H84V4H88V14ZM92 4H90V2H82V4H80V14H82V16H90V14H92V4Z" fill="black"/>
          <path fill-rule="evenodd" clip-rule="evenodd" d="M135 4H134V5H133V6H134V5H135V4ZM124 5H123V6H124V5ZM144 5H145V6H144V5ZM144 5H137V4H144V5ZM145 12V6H146V12H145ZM144 13V12H145V13H144ZM144 13V14H143V13H144ZM153 7H152V8H153V10H152V11H153V10H154V11H155V10H156V8H155V7H154V8H153V7ZM154 8V10H155V8H154ZM128 7H127V10H128V12H130V10H131V7H130V10H128V7ZM151 8H148V9H151V8Z" fill="black"/>
        </svg>
        <p>loading...</p>
      </div>
    </div>

  </div>
</template>
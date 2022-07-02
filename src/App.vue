<template>
  <div class="h-screen flex items-center" >
     <Title>
      <template #body>picoseco</template>
    </Title>

    <Renderer ref="rendererRef" antialias shadow resize :orbit-ctrl="{ enableDamping: true, dampingFactor: 0.05 }">
      <Camera ref="cameraRef" :position="{ x: -0.4, y: 0.4, z: 0.3 }" />
      <Scene ref="sceneRef" background="#dee">
        <PointLight cast-shadow ref="light1" color="#fff" :intensity="2" :shadow-map-size="{ width:1024, height:1024 }" :position="{ x: 1, y: 5, z: 5 }" />

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

<script setup>
  import { ref, onMounted, onBeforeMount } from "vue"
  import { Clock, ACESFilmicToneMapping, Vector3, sRGBEncoding, EquirectangularReflectionMapping, MeshPhysicalMaterial } from "three"
  import { RGBELoader } from "three/examples/jsm/loaders/RGBELoader"
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

  var audio0,
  light,
  gltfScene,
  camera,
  orbitCtrl,
  renderer

var clock = new Clock()

const rendererRef = ref()
const sceneRef = ref()
const cameraRef = ref()
const gltfRef = ref()
// const areaLightL = ref()
// const target = new Vector3(0, 0, 0)
const hdrimgUrl = '/Studio_80s.hdr'

  onBeforeMount(() => {
    console.clear()
  })

  onMounted(() => {
    renderer = rendererRef.value.renderer

    renderer.outputEncoding = sRGBEncoding
    renderer.toneMapping = ACESFilmicToneMapping
    renderer.toneMappingExposure = 1
    // turn on the physically correct lighting model
    renderer.physicallyCorrectLights = true
    // light.lookAt(0, 0, 0)
    // light2.lookAt(0, 0, 0)
    // resiser (resize the canvas and composer)
    // const resizer = new Resizer(camera, renderer, composer)
  })

  function onReady(gltf) {
  gltfScene = gltf.scene
  const exprimidor = gltfScene.children[2]
  // car paint
  let material = new MeshPhysicalMaterial( {
    clearcoat: 1.0,
    clearcoatRoughness: 0.1,
    metalness: 0.2,
    roughness: 0.7,
    color: 0xffffff,
    // normalMap: normalMap3,
    // normalScale: new THREE.Vector2( 0.15, 0.15 )
  } )
  exprimidor.material = material

  // self shadowing
  exprimidor.receiveShadow = true
  exprimidor.castShadow = true

  // environment
  const loadedTexture = new RGBELoader().load(hdrimgUrl, () => {
    const envMap = loadedTexture
    envMap.mapping = EquirectangularReflectionMapping
    exprimidor.material.envMap = envMap
  })

  console.log(exprimidor)
  animate()
}

function animate() {
  requestAnimationFrame(animate)
  // rotate scene
  gltfScene.rotation.y = Math.PI * Math.sin(clock.getElapsedTime() / 5)
  //FX render pass
  // composer.render()
}
</script>
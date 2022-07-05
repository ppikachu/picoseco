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
// const areaLightL = ref()
// const target = new Vector3(0, 0, 0)

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
    // light.lookAt(0, 0, 0)
    // light2.lookAt(0, 0, 0)
    // resiser (resize the canvas and composer)
    // const resizer = new Resizer(camera, renderer, composer)
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

  /* // standard
  const material = new MeshStandardMaterial({
    color: 0x00ffff,
    metalness: 0.2,
    roughness: 0.2,
  }) */

  /* // Subsurface Scattering
  const loader = new TextureLoader()
  const imgTexture = loader.load( '/exp_ambient_occlusion.jpg' )
  const thicknessTexture = loader.load( '/exp_thickness.jpg' )
  // thicknessTexture.mapping = UVMapping
  thicknessTexture.flipX = true
  thicknessTexture.flipY = true

  imgTexture.wrapS = imgTexture.wrapT = RepeatWrapping

  const shader = SubsurfaceScatteringShader
  const uniforms = UniformsUtils.clone( shader.uniforms )

  uniforms[ 'map' ].value = imgTexture

  uniforms[ 'diffuse' ].value = new Vector3( 0.8, 1.0, 1.0 )
  uniforms[ 'shininess' ].value = 500

  uniforms[ 'thicknessMap' ].value = thicknessTexture;
  uniforms[ 'thicknessColor' ].value = new Vector3( 0.1, 0.2, 0.5 );
  uniforms[ 'thicknessDistortion' ].value = 0.1;
  uniforms[ 'thicknessAmbient' ].value = 0.4;
  uniforms[ 'thicknessAttenuation' ].value = 0.8;
  uniforms[ 'thicknessPower' ].value = 2.0;
  uniforms[ 'thicknessScale' ].value = 16.0;

  const material = new ShaderMaterial( {
    uniforms: uniforms,
    vertexShader: shader.vertexShader,
    fragmentShader: shader.fragmentShader,
    lights: true
  } )
  material.extensions.derivatives = true */

  // self shadowing
  // exprimidor.receiveShadow = true
  // exprimidor.castShadow = true

  // environment
  const hdrimgUrl = '/Studio_80s.hdr'
  const scene = sceneRef.value.scene
	scene.fog = new Fog( 0xddeeee, 0.45, 0.8 )
  // scene.fog = new FogExp2( 0xddeeee, 2 )
  const loadedTexture = new RGBELoader().load(hdrimgUrl, () => {
    const envMap = loadedTexture
    scene.environment = envMap
    scene.environment.mapping = EquirectangularReflectionMapping
    exprimidor.material.envMap = envMap
    // exprimidor.material.mapping = UVMapping
    // exprimidor.material.map.flipY = true
    exprimidor.material.needsUpdate = true
  })

  exprimidor.material = material
  // console.log(scene, exprimidor)

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

<template>
  <div class="h-screen flex items-center" >
     <Title>
      <!-- <template #body>picoseco</template> -->
    </Title>

    <Renderer ref="rendererRef" antialias shadow resize pointer :orbit-ctrl="{ enableDamping: true, dampingFactor: 0.05 }">
      <Camera ref="cameraRef" :position="{ x: -0.4, y: 0.4, z: 0.3 }" />
      <Scene ref="sceneRef" background="#dee">
        <!-- <PointLight helper cast-shadow ref="light1" color="#fff" :intensity="2" :shadow-map-size="{ width:1024, height:1024 }" :position="{ x: 1, y: 5, z: 5 }" /> -->
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
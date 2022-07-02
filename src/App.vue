<template>
  <div class="h-screen flex items-center bg-gradient-to-b from-cyan-900 to-pink-900" >
    <Renderer ref="rendererRef" antialias shadow resize :orbit-ctrl="{ enableDamping: true, dampingFactor: 0.05 }">
      <Camera ref="cameraRef" :position="{ x: 0, y: 0.5, z: 1 }" />
      <Scene ref="sceneRef" background="#ffffff">
        <PointLight cast-shadow ref="light1" color="#fff" :intensity="2" :position="{ x: 1, y: 5, z: 5 }" />
        <PointLight ref="light2" color="#1CD1E1" :intensity="0.85" :position="{ x: 5, y: 5, z: 10 }" />
        <PointLight ref="light3" color="#18C02C" :intensity="0.85" :position="{ x: 0, y: 5, z: -10 }" />
        <PointLight ref="light4" color="#ee3bcf" :intensity="0.85" :position="{ x: 5, y: -5, z: 10 }" />

        <GltfModel
          ref="gltfRef"
          src="/exp.gltf"
          @load="onReady"
        />

      </Scene>
    </Renderer>
  </div>
</template>

<script setup>
  import { ref, onMounted, onBeforeMount } from "vue"

  import { Clock, ACESFilmicToneMapping, Vector3 } from "three"

  import {
    Camera,
    PhysicalMaterial,
    GltfModel,
    PointLight,
    Renderer,
    Scene,
    StandardMaterial,
    TorusGeometry,
    EffectPass,
  } from 'troisjs'

  var audio0,
  mixer,
  light,
  light2,
  gltfScene,
  composer,
  camera,
  orbitCtrl,
  renderer,
  mesh

var clock = new Clock()

const rendererRef = ref()
const sceneRef = ref()
const cameraRef = ref()
const gltfRef = ref()
// const areaLightL = ref();
// const areaLightH = ref();
// const target = new Vector3(0, 0, 0);

  onBeforeMount(() => {
    console.clear()
  })

  onMounted(() => {
    renderer = rendererRef.value.renderer
    mesh = gltfRef.value.mesh

    //renderer.outputEncoding = sRGBEncoding
    // renderer.toneMapping = ACESFilmicToneMapping;
    // turn on the physically correct lighting model
    // renderer.physicallyCorrectLights = true;

    //#region postprocessing
    // composer = new EffectComposer(renderer, {
    //   multisampling: 2,
    // });
    // shockWaveEffect = new ShockWaveEffect(camera, target, {
    //   speed: 2,
    //   maxRadius: 0.15,
    //   waveSize: 0.05,
    //   amplitude: 0.03,
    // });
    // const bloomEffect = new BloomEffect({
    //   luminanceThreshold: 0.7,
    //   intensity: 1,
    // });
    // const vignetteEffect = new VignetteEffect();
    // const renderPass = new RenderPass(scene, camera);
    // const effectPass = new EffectPass(
    //   camera,
    //   shockWaveEffect,
    //   bloomEffect,
    // );
    // apply postprocessing
    // composer.addPass(renderPass);
    // composer.addPass(effectPass);
    //#endregion
    // light.lookAt(0, 0, 0)
    // light2.lookAt(0, 0, 0)
    // resiser (resize the canvas and composer)
    // const resizer = new Resizer(camera, renderer, composer)
  })

  function onReady(gltf) {
  gltfScene = gltf.scene
  // self shadowing
  const exprimidor = gltfRef.value
  exprimidor.receiveShadow = true
  exprimidor.castShadow = true
  /*// environment
    const loadedTexture = new RGBELoader().load(hdrimgUrl, () => {
    const envMap = loadedTexture
    envMap.mapping = EquirectangularReflectionMapping
    exprimidor.material.envMap = envMap
    piezaLight.material.envMap = envMap
  })*/
  // animation
  // mixer = new AnimationMixer(gltf.scene)
  // mixer.clipAction(gltf.animations[0]).play()
  animate()
}

function animate() {
  requestAnimationFrame(animate);
  // rotate scene
  gltfScene.rotation.y = Math.PI * Math.sin(clock.getElapsedTime() / 5)
  //FX render pass
  // composer.render();
}

</script>

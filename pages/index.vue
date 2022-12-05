<template>
  <canvas ref="canvas"></canvas>
  <div
    id="container"
    class="text-white absolute text-center w-full max-w-2xl px-5"
    style="top: 50%; transform: translate(-50%, -50%); left: 50%"
  >
    <h1
      id="zwn28"
      class="font-space-mono text-lg mb-2 opacity-0"
      style="transform: translateY(30px)"
    >
      zwn28
    </h1>
    <p
      id="oneWithAn"
      class="select-none font-exo text-4xl opacity-0"
      style="transform: translateY(30px)"
    >
      ONE WITH AN EVERLASTING DESIRE FOR THE UNKNOWN & UNTOLD
    </p>
    <button
      style="transform: translateY(30px)"
      id="viewWorkBtn"
      class="border px-4 py-2 rounded-lg text-sm font-space-mono uppercase mt-8 opacity-0 hover:bg-white hover:text-gray-900"
    >
      View Work
    </button>
  </div>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Exo+2:ital,wght@1,700&family=Space+Mono&display=swap');
.font-exo {
  font-family: 'Exo 2', sans-serif;
}

.font-space-mono {
  font-family: 'Space Mono', monospace;
}

body {
  -webkit-font-smoothing: antialiased;
}
</style>

<script setup>
//import three js
// import * as dat from 'dat.gui'
import gsap from 'gsap'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'
import {
  BoxGeometry,
  BufferAttribute,
  BufferGeometry,
  DirectionalLight,
  DoubleSide,
  Float32BufferAttribute,
  Mesh,
  MeshBasicMaterial,
  MeshPhongMaterial,
  PerspectiveCamera,
  PlaneGeometry,
  Points,
  PointsMaterial,
  Raycaster,
  Scene,
  WebGLRenderer,
} from 'three'

const canvas = ref(null)
onMounted(() => {
  const router = useRouter()
  const raycaster = new Raycaster()
  // console.log(OrbitControls)
  // const gui = new dat.GUI()
  const world = {
    plane: {
      width: 400,
      height: 400,
      widthSegments: 50,
      heightSegments: 50,
    },
  }
  function generatePlane() {
    planeMesh.geometry.dispose()
    planeMesh.geometry = new PlaneGeometry(
      world.plane.width,
      world.plane.height,
      world.plane.widthSegments,
      world.plane.heightSegments
    )
    const colors = []
    for (let i = 0; i < planeMesh.geometry.attributes.position.count; i++) {
      colors.push(0, 0.19, 0.4)
    }
    planeMesh.geometry.setAttribute(
      'color',
      new BufferAttribute(new Float32Array(colors), 3)
    )

    const { array } = planeMesh.geometry.attributes.position

    const randomValues = []
    for (let i = 0; i < array.length; i++) {
      if (i % 3 === 0) {
        const x = array[i]
        const y = array[i + 1]
        const z = array[i + 2]
        array[i] = x + (Math.random() - 0.5) * 3
        array[i + 1] = y + (Math.random() - 0.5) * 3
        array[i + 2] = z + (Math.random() - 0.5) * 3
      }
      randomValues.push(Math.random() * Math.PI * 2)
    }

    planeMesh.geometry.attributes.position.randomValues = randomValues
    planeMesh.geometry.attributes.position.originalPosition =
      planeMesh.geometry.attributes.position.array
  }
  // gui.add(world.plane, 'width', 1, 500).onChange(() => {
  //   generatePlane()
  // })
  // gui.add(world.plane, 'height', 1, 500).onChange(() => {
  //   generatePlane()
  // })

  // gui.add(world.plane, 'widthSegments', 1, 100).onChange(() => {
  //   generatePlane()
  // })

  // gui.add(world.plane, 'heightSegments', 1, 100).onChange(() => {
  //   generatePlane()
  // })

  const scene = new Scene()
  const camera = new PerspectiveCamera(75, innerWidth / innerHeight, 0.1, 1000)
  const renderer = new WebGLRenderer({
    canvas: canvas.value,
  })

  renderer.setSize(innerWidth, innerHeight)
  renderer.setPixelRatio(devicePixelRatio)

  const planeGeometry = new PlaneGeometry(
    world.plane.width,
    world.plane.height,
    world.plane.widthSegments,
    world.plane.heightSegments
  )
  const planeMaterial = new MeshPhongMaterial({
    side: DoubleSide,
    flatShading: true,
    vertexColors: true,
  })

  const planeMesh = new Mesh(planeGeometry, planeMaterial)
  scene.add(planeMesh)

  generatePlane()

  const light = new DirectionalLight(0xffffff, 1)
  light.position.set(0, -1, 1)
  scene.add(light)

  const backLight = new DirectionalLight(0xffffff, 1)
  backLight.position.set(0, 0, -1)
  scene.add(backLight)

  new OrbitControls(camera, renderer.domElement)
  camera.position.z = 50

  const mouse = {
    x: undefined,
    y: undefined,
  }

  const starGeometry = new BufferGeometry()
  const starMaterial = new PointsMaterial({ color: 0xffffff })

  const starVertices = []
  for (let i = 0; i < 10000; i++) {
    const x = (Math.random() - 0.5) * 2000
    const y = (Math.random() - 0.5) * 2000
    const z = (Math.random() - 0.5) * 2000
    starVertices.push(x, y, z)
  }

  starGeometry.setAttribute(
    'position',
    new Float32BufferAttribute(starVertices, 3)
  )

  const stars = new Points(starGeometry, starMaterial)
  scene.add(stars)
  let frame = 0

  function animate() {
    requestAnimationFrame(animate)
    renderer.render(scene, camera)
    // planeMesh.rotation.x += 0.01
    raycaster.setFromCamera(mouse, camera)
    frame += 0.01
    const { array, originalPosition, randomValues } =
      planeMesh.geometry.attributes.position
    for (let i = 0; i < array.length; i += 3) {
      //x
      array[i] = originalPosition[i] + Math.cos(frame + randomValues[i]) * 0.01
      //y
      array[i + 1] =
        originalPosition[i + 1] + Math.sin(frame + randomValues[i + 1]) * 0.001
      //z
      // array[i + 2] = originalPosition[i + 2] + Math.sin(frame + randomValues[i + 2]) * 0.003
    }
    planeMesh.geometry.attributes.position.needsUpdate = true
    const intersects = raycaster.intersectObject(planeMesh)
    if (intersects.length) {
      const { color } = intersects[0].object.geometry.attributes

      const initialColor = {
        r: 0,
        g: 0.19,
        b: 0.4,
      }
      const hoverColor = {
        r: 0.1,
        g: 0.5,
        b: 1,
      }

      gsap.to(hoverColor, {
        r: initialColor.r,
        g: initialColor.g,
        b: initialColor.b,
        onUpdate: () => {
          color.setX(intersects[0].face.a, hoverColor.r)
          color.setY(intersects[0].face.a, hoverColor.g)
          color.setZ(intersects[0].face.a, hoverColor.b)
          //vertex 2
          color.setX(intersects[0].face.b, hoverColor.r)
          color.setY(intersects[0].face.b, hoverColor.g)
          color.setZ(intersects[0].face.b, hoverColor.b)

          // vertex 3
          color.setX(intersects[0].face.c, hoverColor.r)
          color.setY(intersects[0].face.c, hoverColor.g)
          color.setZ(intersects[0].face.c, hoverColor.b)
          color.needsUpdate = true
        },
      })
    }
    stars.rotation.x += 0.0005
  }

  animate()

  addEventListener('mousemove', (event) => {
    //normalized coordinates
    mouse.x = (event.clientX / innerWidth) * 2 - 1
    mouse.y = -(event.clientY / innerHeight) * 2 + 1
    // console.log(mouse)
  })

  gsap.to('#zwn28', {
    opacity: 1,
    duration: 1.5,
    y: 0,
    ease: 'expo',
  })
  gsap.to('#oneWithAn', {
    opacity: 1,
    duration: 1.5,
    delay: 0.3,
    y: 0,
    ease: 'expo',
  })

  gsap.to('#viewWorkBtn', {
    opacity: 1,
    duration: 1.5,
    delay: 0.6,
    y: 0,
    ease: 'expo',
  })
  document.getElementById('viewWorkBtn').addEventListener('click', () => {
    gsap.to('#container', {
      opacity: 0,
    })

    gsap.to(camera.position, {
      z: 25,
      ease: 'power3.inOut',
      duration: 2,
    })

    gsap.to(camera.rotation, {
      x: 1.57,
      ease: 'power3.inOut',
      duration: 2,
    })

    gsap.to(camera.position, {
      delay: 1.5,
      y: 1000,
      ease: 'power3.in',
      duration: 1.5,
      onComplete: () => {
        //route to a the work page nuxt3
        router.push({ path: '/work' })
      },
    })
  })

  addEventListener('resize', () => {
    camera.aspect = innerWidth / innerHeight
    camera.updateProjectionMatrix()
    renderer.setSize(innerWidth, innerHeight)
  })

  // addEventListener('mousemove', (e) => {
  //   const { clientX, clientY } = e
  //   const x = (clientX / innerWidth) * 2 - 1
  //   const y = -(clientY / innerHeight) * 2 + 1
  //   planeMesh.rotation.x = y * Math.PI * 2
  //   planeMesh.rotation.y = x * Math.PI * 2
  // })
})
</script>

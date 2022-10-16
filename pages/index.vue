<template>
  <div
    class="h-full flex justify-center items-center overflow-hidden bg-primary"
  >
    <div id="threejs">
      <div id="dev">
        <div
          v-show="DevIsHidden"
          class="z-50 absolute text-center right-2.5 w-80 top-2.5 block"
        >
          <div>
            <p class="inline">Camera Rotation (XYZ):</p>
            <p id="cam_rot" class="inline"></p>
          </div>
          <div>
            <p class="inline">Camera Position (XYZ):</p>
            <p id="cam_pos" class="inline"></p>
          </div>
        </div>
      </div>
      <div class="z-50 absolute text-center left-2.5 top-2.5 block">
        <div>
          <button class="block" @click="DevIsHidden = !DevIsHidden">
            Dev
          </button>
        </div>
      </div>
      <div id="name" class="z-50 absolute text-center top-2.5 w-full block"></div>
      <div id="information" class="z-50 absolute text-center top-2.5 right-2.5 block">
        <div class="grid">
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
        </div>
      </div>
      <canvas id="canvas"> </canvas>
      <div id="labels"></div>
    </div>
  </div>
</template>

<script>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";
import { InteractionManager } from "three.interactive";

export default {
  mounted() {
    // scene
    const scene = new THREE.Scene();

    // <div id="labels" class="z-50 absolute text-center top-2.5 w-full block">Discription</div>

    // camera
    const camera = new THREE.PerspectiveCamera(
      60,
      window.innerWidth / window.innerHeight,
      0.1,
      999999
    );

    // renderer
    const renderer = new THREE.WebGLRenderer({
      canvas: document.querySelector("#canvas"),
    });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setClearColor(0x000000, 0);

    const interactionManager = new InteractionManager(
      renderer,
      camera,
      renderer.domElement
    );

    const cam_rot = document.querySelector("#cam_rot");
    const cam_pos = document.querySelector("#cam_pos");
    const name_planet = document.querySelector("#name");

    // Labels
    const labelContainerElem = document.querySelector("#labels");
    const elem = document.createElement("div");
    elem.textContent = name;
    labelContainerElem.appendChild(elem);

    // controls
    const controls = new OrbitControls(camera, renderer.domElement);
    controls.minDistance = 40;
    controls.maxDistance = 140;

    camera.position.set(0, 140, 0);
    controls.update();
    // controls.enabled = false;

    // light
    const ambientLight = new THREE.AmbientLight(0x404040);

    scene.add(ambientLight);

    const spotLight = new THREE.SpotLight(0xffffff);
    spotLight.position.set(10, 10, 10);
    scene.add(spotLight);
    // helpers

    const spotLightHelper = new THREE.SpotLightHelper(spotLight);
    // scene.add(spotLightHelper);

    const size = 200;
    const divisions = 200;

    // const gridHelper = new THREE.GridHelper(size, divisions);
    // scene.add(gridHelper);
    const axesHelper = new THREE.AxesHelper(5);
    scene.add(axesHelper);

    // model
    // const gltfLoader = new GLTFLoader();
    // gltfLoader.load("models/scene.gltf", (gltf) => {
    //   gltf.scene.traverse((child) => {
    //     if (child.material) child.material.metalness = 0;
    //     child.position.x=0;
    //     child.position.z=0;
    //     child.position.y=0;
    //   });
    //   scene.add(gltf.scene);
    // });

    const ring1 = new THREE.RingGeometry(21.4, 21.5, 128);
    const ring2 = new THREE.RingGeometry(31.6, 31.7, 128);
    const ring3 = new THREE.RingGeometry(72.4, 72.5, 128);
    const ring4 = new THREE.RingGeometry(48.3, 48.4, 128);

    const geometry = new THREE.SphereGeometry(6, 16, 16);
    const planetsphere = new THREE.SphereGeometry(1, 128, 128);
    const moonsphere = new THREE.SphereGeometry(0.005, 16, 128);

    const material = new THREE.MeshBasicMaterial({ color: 0xffff00 });
    const planet_material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
    const moon_material = new THREE.MeshBasicMaterial({ color: 0x63fffa });

    const ring_material = new THREE.MeshBasicMaterial({
      color: 0x6600ff,
      side: THREE.DoubleSide,
    });

    const ring1_Mesh = new THREE.Mesh(ring1, ring_material);
    const ring2_Mesh = new THREE.Mesh(ring2, ring_material);
    const ring3_Mesh = new THREE.Mesh(ring3, ring_material);
    const ring4_Mesh = new THREE.Mesh(ring4, ring_material);

    const rings = new THREE.Group();
    rings.add(ring1_Mesh);
    rings.add(ring2_Mesh);
    rings.add(ring3_Mesh);
    rings.add(ring4_Mesh);

    rings.rotation.x = Math.PI / 2;
    scene.add(rings);

    const Crusader = new THREE.Mesh(planetsphere, planet_material);
    Crusader.position.set(-31.60362666666665, 0, -4.4416);
    Crusader.name = "Crusader";

    const microTech = new THREE.Mesh(planetsphere, planet_material);
    microTech.position.set(37.43680875414135, 0, 61.97624160805);
    microTech.name = "microTech";

    const ArcCorp = new THREE.Mesh(planetsphere, planet_material);
    ArcCorp.position.set(30.97944123309335, 0, -36.91986153385435);
    ArcCorp.name = "ArcCorp";

    const Hurston = new THREE.Mesh(planetsphere, planet_material);
    Hurston.position.set(0, 0, 21.41742848833335);
    Hurston.name = "Hurston";

    const planets = new THREE.Group();
    planets.add(Crusader);
    planets.add(microTech);
    planets.add(ArcCorp);
    planets.add(Hurston);

    scene.add(planets);

    // Star
    const stanton_star = new THREE.Mesh(geometry, material);
    scene.add(stanton_star);

    //planet InteractionManager
    interactionManager.add(Crusader);
    interactionManager.add(microTech);
    interactionManager.add(ArcCorp);
    interactionManager.add(Hurston);
    interactionManager.add(stanton_star);

    //planet Interactions
    for (let i in planets.children) {
      planets.children[i].addEventListener("mouseover", (e) => {
        document.body.style.cursor = "pointer";
      });

      planets.children[i].addEventListener("mouseout", (e) => {
        document.body.style.cursor = "default";
      });
    }

    // console.log(Crusader.position);

    Crusader.addEventListener("click", (e) => {
      controls.target = Crusader.position;
      controls.minDistance = 3;
      controls.maxDistance = 10;
      controls.update();
      rings.visible = false;
      planets.children[1].visible = false;
      planets.children[2].visible = false;
      planets.children[3].visible = false;
      name_planet.textContent = "Crusader";
    });

    microTech.addEventListener("click", (e) => {
      controls.target = microTech.position;
      controls.minDistance = 3;
      controls.maxDistance = 10;
      controls.update();
      rings.visible = false;
      planets.children[0].visible = false;
      planets.children[2].visible = false;
      planets.children[3].visible = false;
      name_planet.textContent = "microTech";
    });

    ArcCorp.addEventListener("click", (e) => {
      controls.target = ArcCorp.position;
      controls.minDistance = 3;
      controls.maxDistance = 10;
      controls.update();
      rings.visible = false;
      planets.children[0].visible = false;
      planets.children[1].visible = false;
      planets.children[3].visible = false;
      name_planet.textContent = "ArcCorp";
    });

    Hurston.addEventListener("click", (e) => {
      controls.target = Hurston.position;
      controls.minDistance = 3;
      controls.maxDistance = 10;
      controls.update();
      rings.visible = false;
      planets.children[0].visible = false;
      planets.children[1].visible = false;
      planets.children[2].visible = false;
      name_planet.textContent = "Hurston";
    });

    stanton_star.addEventListener("click", (e) => {
      controls.target = stanton_star.position;
      controls.minDistance = 40;
      controls.maxDistance = 140;
      controls.update();
      rings.visible = true;
      planets.children[0].visible = true;
      planets.children[1].visible = true;
      planets.children[2].visible = true;
      planets.children[3].visible = true;
      name_planet.textContent = "";
    });

    // Moons
    const OOC_Stanton_2b_Daymar = new THREE.Mesh(moonsphere, moon_material);
    OOC_Stanton_2b_Daymar.position.set(
      -31.55089923255805,
      0,
      -4.35026460916426
    );

    const moons = new THREE.Group();
    moons.add(OOC_Stanton_2b_Daymar);

    console.log(camera.position);
    console.log(camera.rotation);

    // scene.add(moons);

    // animation
    const tick = () => {
      controls.update();
      renderer.render(scene, camera);
      window.requestAnimationFrame(tick);
      interactionManager.update();

      let cam_rot_x = JSON.stringify(Math.round(camera.rotation.x * 10) / 10);
      let cam_rot_y = JSON.stringify(Math.round(camera.rotation.y * 10) / 10);
      let cam_rot_z = JSON.stringify(Math.round(camera.rotation.z * 10) / 10);
      let cam_rot_xyz = cam_rot_x + " " + cam_rot_y + " " + cam_rot_z;
      cam_rot.textContent = cam_rot_xyz;

      let cam_pos_x = JSON.stringify(Math.round(camera.position.x * 10) / 10);
      let cam_pos_y = JSON.stringify(Math.round(camera.position.y * 10) / 10);
      let cam_pos_z = JSON.stringify(Math.round(camera.position.z * 10) / 10);
      let cam_pos_xyz = cam_pos_x + " " + cam_pos_y + " " + cam_pos_z;
      cam_pos.textContent = cam_pos_xyz;

      

    };
    tick();
  },
  el: "#app",
  data() {
    return {
      DevIsHidden: false,
    };
  },
};
</script>

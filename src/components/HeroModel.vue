<script setup lang="ts">
import { onBeforeUnmount, onMounted, ref, watch } from "vue";
import * as THREE from "three";
const props = defineProps<{ slide: number }>();
const canvas = ref<HTMLCanvasElement>();
let renderer: THREE.WebGLRenderer | undefined,
  frame = 0,
  selected: THREE.Group[] = [];
const orange = 0xe2560f,
  edgeMat = new THREE.LineBasicMaterial({ color: 0x91a0ad }),
  orangeEdge = new THREE.LineBasicMaterial({ color: orange });
const edgeBox = (w: number, h: number, d: number, color = edgeMat) =>
  new THREE.LineSegments(
    new THREE.EdgesGeometry(new THREE.BoxGeometry(w, h, d)),
    color,
  );
const material = (color: number) =>
  new THREE.MeshStandardMaterial({ color, metalness: 0.38, roughness: 0.52 });
const setSlide = (n: number) =>
  selected.forEach((group, i) => {
    group.visible = i === n;
  });
watch(() => props.slide, setSlide);
onMounted(() => {
  const el = canvas.value!,
    scene = new THREE.Scene(),
    camera = new THREE.PerspectiveCamera(38, 1, 0.1, 400);
  camera.position.set(35, 20, 45);
  camera.lookAt(0, 0, 0);
  renderer = new THREE.WebGLRenderer({
    canvas: el,
    alpha: true,
    antialias: true,
  });
  renderer.setPixelRatio(Math.min(devicePixelRatio, 2));
  renderer.outputColorSpace = THREE.SRGBColorSpace;
  scene.add(new THREE.AmbientLight(0xffffff, 1.65));
  const light = new THREE.DirectionalLight(0xffffff, 2.2);
  light.position.set(18, 28, 15);
  scene.add(light);
  const warm = new THREE.DirectionalLight(orange, 1.8);
  warm.position.set(-18, 12, -10);
  scene.add(warm);
  // 01 — roof truss / steel structure
  const roof = new THREE.Group();
  for (let z = -10; z <= 10; z += 5) {
    for (const s of [-1, 1]) {
      for (let i = 0; i < 5; i++) {
        const b = edgeBox(4.5, 0.34, 0.34, orangeEdge);
        b.position.set(s * (2.2 + i * 2.1), 6 - i * 0.88, z);
        b.rotation.z = -s * 0.2;
        roof.add(b);
      }
      const col = edgeBox(0.62, 9, 0.62);
      col.position.set(s * 11, -1, z);
      roof.add(col);
    }
    const chord = edgeBox(22, 0.36, 0.36);
    chord.position.set(0, 2.5, z);
    roof.add(chord);
  }
  for (const x of [-5, 5]) {
    const p = edgeBox(0.2, 0.2, 24, orangeEdge);
    p.position.set(x, 5.4, 0);
    roof.add(p);
  }
  roof.position.set(8, -1, 0);
  roof.rotation.set(-0.12, -0.54, 0);
  scene.add(roof);
  // 02 — industrial building
  const plant = new THREE.Group();
  const slab = new THREE.Mesh(
    new THREE.BoxGeometry(30, 0.45, 24),
    material(0xced3d4),
  );
  slab.position.y = -3;
  plant.add(slab);
  const shell = new THREE.Mesh(
    new THREE.BoxGeometry(20, 7, 14),
    material(0x526272),
  );
  shell.position.y = 0.5;
  plant.add(shell);
  const shellEdge = edgeBox(20, 7, 14);
  shellEdge.position.y = 0.5;
  plant.add(shellEdge);
  const roofCap = new THREE.Mesh(
    new THREE.CylinderGeometry(7.2, 7.2, 20, 24, 1, false, 0, Math.PI),
    material(0x9aa6af),
  );
  roofCap.rotation.z = Math.PI / 2;
  roofCap.position.y = 4;
  plant.add(roofCap);
  const office = new THREE.Mesh(
    new THREE.BoxGeometry(6, 4.5, 6),
    material(0x657689),
  );
  office.position.set(-13, -0.6, 3);
  plant.add(office);
  for (let i = 0; i < 4; i++) {
    const win = new THREE.Mesh(
      new THREE.BoxGeometry(3.1, 0.85, 0.18),
      material(orange),
    );
    win.position.set(-4 + i * 3.4, 2.3, 7.05);
    plant.add(win);
  }
  plant.position.set(8, -1, 0);
  plant.rotation.set(-0.13, -0.54, 0);
  scene.add(plant);
  // 03 — layered road with orange safety guardrails
  const road = new THREE.Group();
  let y = 1;
  for (const layer of [
    { t: 0.9, c: 0x343a42, w: 26 },
    { t: 1.2, c: 0x68737e, w: 27.5 },
    { t: 1.6, c: 0xa6a08f, w: 29 },
    { t: 2.2, c: 0xd1c8b3, w: 30.5 },
  ]) {
    const m = new THREE.Mesh(
      new THREE.BoxGeometry(layer.w, layer.t, 16),
      material(layer.c),
    );
    y -= layer.t / 2;
    m.position.y = y;
    y -= layer.t / 2;
    road.add(m);
    const e = edgeBox(layer.w, layer.t, 16);
    e.position.y = m.position.y;
    road.add(e);
  }
  for (let i = -2; i <= 2; i++) {
    const dash = new THREE.Mesh(
      new THREE.BoxGeometry(0.5, 0.12, 2.4),
      material(0xffffff),
    );
    dash.position.set(0, 1.05, i * 4);
    road.add(dash);
  }
  const rail = material(orange);
  for (const s of [-1, 1]) {
    for (let z = -7; z <= 7; z += 3.5) {
      const p = new THREE.Mesh(
        new THREE.CylinderGeometry(0.14, 0.14, 2.4, 8),
        rail,
      );
      p.position.set(s * 14, 1.9, z);
      road.add(p);
    }
    const r = new THREE.Mesh(new THREE.BoxGeometry(0.2, 0.2, 16), rail);
    r.position.set(s * 14, 2.7, 0);
    road.add(r);
  }
  road.position.set(10, 0.5, 0);
  road.rotation.set(-0.16, -0.5, 0);
  scene.add(road);
  selected = [roof, plant, road];
  setSlide(props.slide);
  const current = () => selected[props.slide] ?? selected[0]!;
  let drag = false,
    last = 0;
  el.onpointerdown = (e) => {
    drag = true;
    last = e.clientX;
    el.setPointerCapture(e.pointerId);
  };
  el.onpointermove = (e) => {
    if (drag) {
      current().rotation.y += (e.clientX - last) * 0.008;
      last = e.clientX;
    }
  };
  el.onpointerup = () => (drag = false);
  const resize = () => {
    const r = el.getBoundingClientRect();
    renderer!.setSize(r.width, r.height, false);
    camera.aspect = r.width / r.height;
    camera.updateProjectionMatrix();
  };
  new ResizeObserver(resize).observe(el);
  resize();
  const radiansPerSecond = (6 * Math.PI * 2) / 13;
  let lastFrame = performance.now();
  const animate = (now: number) => {
    frame = requestAnimationFrame(animate);
    const elapsedSeconds = Math.min((now - lastFrame) / 1000, 0.1);
    lastFrame = now;
    if (!drag) current().rotation.y += radiansPerSecond * elapsedSeconds;
    renderer!.render(scene, camera);
  };
  frame = requestAnimationFrame(animate);
});
onBeforeUnmount(() => {
  cancelAnimationFrame(frame);
  renderer?.dispose();
});
</script>
<template>
  <canvas ref="canvas" class="model-canvas" aria-label="โมเดลวิศวกรรมสามมิติ" />
</template>

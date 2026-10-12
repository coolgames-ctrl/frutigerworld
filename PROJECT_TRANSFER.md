# 🌐 FRUTIGER AERO WORLD EXPLORER — COMPLETE PROJECT TRANSFER FILE

> **AI INSTRUCTIONS**:  
> This file contains the complete source code, asset processing pipelines, 3D math formulas, and architectural specification for the **Frutiger Aero World Explorer**. Any AI assistant reading this file can recreate the entire project with 100% fidelity.

---

## 📋 Table of Contents
1. [Project Overview & Aesthetic](#1-project-overview--aesthetic)
2. [Directory Structure](#2-directory-structure)
3. [Quick Setup & Run Commands](#3-quick-setup--run-commands)
4. [Source Code Files](#4-source-code-files)
   - [`package.json`](#packagejson)
   - [`index.html`](#indexhtml)
   - [`src/style.css`](#srcstylecss)
   - [`src/main.js`](#srcmainjs)
5. [Asset Generation & Python Image Pipelines](#5-asset-generation--python-image-pipelines)
   - [Separating Wind Turbine Blades & Tower](#separating-wind-turbine-blades--tower)
   - [City Skyline Alpha Ground Blend](#city-skyline-alpha-ground-blend)
6. [Core Mathematical Formulations](#6-core-mathematical-formulations)
   - [Rolling Hills Heightmap](#rolling-hills-heightmap)
   - [Camera Look-Around & Mobile Touch Controls](#camera-look-around--mobile-touch-controls)
   - [Turbine Rotation & Anchor Placement](#turbine-rotation--anchor-placement)

---

## 1. Project Overview & Aesthetic

- **Theme**: Pure 2000s **Frutiger Aero** (optimistic tech-nature fusion, saturated Windows XP Bliss hills, radiant azure skydome, clean minimalist exploration).
- **Core Experience**: A first-person panoramic world viewer where the user can look around 360° across smooth rolling hills, watching 4 animated wind turbines spin on a distant ridge while a modern glass skyscraper metropolis rises across the horizon.
- **Platform Support**: Desktop (click & drag, mouse wheel FOV zoom) and Mobile (touch drag, two-finger pinch-to-zoom, `touch-action: none`).
- **Tech Stack**: Three.js (WebGL), Vanilla JavaScript (ES Modules), CSS, Vite.

---

## 2. Directory Structure

```
frutigerworld/
├── index.html
├── package.json
├── README.md
├── PROJECT_TRANSFER.md          <-- (This master transfer file)
├── public/
│   └── textures/
│       ├── grass.jpg            <-- Lush green grass seamless texture
│       ├── turbine_tower.png    <-- Wind turbine tower pole & nacelle
│       ├── turbine_blades.png   <-- 900x900 square centered 3-blade rotor
│       └── skyline.png          <-- Frutiger Aero modern city skyline
└── src/
    ├── main.js                  <-- Three.js 3D engine, terrain, animation
    └── style.css                <-- Aero saturated styles, mobile touch rules
```

---

## 3. Quick Setup & Run Commands

```bash
# 1. Initialize and install dependencies
npm install

# 2. Start the local development server
npm run dev

# 3. Build production bundle (optional)
npm run build
```

---

## 4. Source Code Files

### `package.json`
```json
{
  "name": "frutigerworld",
  "version": "1.0.0",
  "description": "Frutiger Aero 3D World Explorer with Three.js",
  "main": "index.js",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "three": "^0.186.1",
    "vite": "^8.3.4"
  }
}
```

---

### `index.html`
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
    <title>Frutiger Aero - World Explorer</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Outfit:wght@400;600&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="/src/style.css" />
  </head>
  <body>
    <!-- Three.js Canvas Container -->
    <div id="canvas-container"></div>

    <!-- Minimal unobtrusive look hint (fades out after first interaction) -->
    <div id="simple-hint" class="simple-hint">
      <div class="hint-pill">
        <span class="hint-icon">✦</span>
        <span>Click &amp; drag to look around</span>
      </div>
    </div>

    <script type="module" src="/src/main.js"></script>
  </body>
</html>
```

---

### `src/style.css`
```css
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  user-select: none;
  -webkit-user-select: none;
}

html, body {
  width: 100%;
  height: 100%;
  overflow: hidden;
  background: #0077c8;
  font-family: 'Outfit', system-ui, -apple-system, sans-serif;
  touch-action: none;
  -webkit-touch-callout: none;
}

#canvas-container {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
  cursor: grab;
  touch-action: none;
  /* Extra saturated, vibrant Frutiger Aero look */
  filter: saturate(1.38) contrast(1.06);
}

#canvas-container:active {
  cursor: grabbing;
}

/* Minimalist elegant floating hint */
.simple-hint {
  position: absolute;
  bottom: 30px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 10;
  pointer-events: none;
  transition: opacity 0.8s ease, transform 0.8s ease;
}

.simple-hint.fade-out {
  opacity: 0;
  transform: translate(-50%, 15px);
}

.hint-pill {
  display: flex;
  align-items: center;
  gap: 8px;
  background: rgba(255, 255, 255, 0.55);
  backdrop-filter: blur(16px) saturate(160%);
  -webkit-backdrop-filter: blur(16px) saturate(160%);
  border: 1px solid rgba(255, 255, 255, 0.85);
  padding: 8px 20px;
  border-radius: 999px;
  color: #01436e;
  font-size: 0.85rem;
  font-weight: 600;
  letter-spacing: 0.4px;
  box-shadow: 0 8px 24px rgba(0, 80, 160, 0.2), inset 0 1px 2px rgba(255, 255, 255, 0.9);
}

.hint-icon {
  color: #0091ea;
  font-size: 0.9rem;
}
```

---

### `src/main.js`
```javascript
import * as THREE from 'three';

// --- TERRAIN HEIGHTMAP MATH ---
// "Flat, nice hills" - smooth rolling harmonic undulations
export function getTerrainHeight(x, z) {
  const h1 = Math.sin(x * 0.011) * Math.cos(z * 0.011) * 11.0;
  const h2 = Math.sin(x * 0.022 + 1.4) * Math.cos(z * 0.019 + 0.6) * 5.0;
  const h3 = Math.cos((x - z) * 0.007) * 8.5;
  const h4 = Math.sin(x * 0.04) * Math.cos(z * 0.04) * 1.8;
  return h1 + h2 + h3 + h4;
}

class WorldExplorer {
  constructor() {
    this.container = document.getElementById('canvas-container');

    // Camera look orientation - centered panoramic framing of both turbines and distant city!
    this.yaw = 0.0;
    this.pitch = 0.02;
    this.targetYaw = 0.0;
    this.targetPitch = 0.02;

    this.isDragging = false;
    this.lastPointerX = 0;
    this.lastPointerY = 0;
    this.hintDismissed = false;

    // Camera vantage position perched atop a gentle hill
    const camX = 0;
    const camZ = 0;
    const camY = getTerrainHeight(camX, camZ) + 2.2;
    this.cameraPos = new THREE.Vector3(camX, camY, camZ);

    this.windTurbines = [];

    // Setup
    this.initScene();
    this.initTerrain();
    this.initSky();
    this.initWindTurbines();
    this.initSkylineCity();
    this.initControls();

    // Loop
    this.clock = new THREE.Clock();
    this.animate = this.animate.bind(this);
    requestAnimationFrame(this.animate);

    window.addEventListener('resize', this.onWindowResize.bind(this));
  }

  initScene() {
    this.scene = new THREE.Scene();

    // Saturated atmospheric fog
    const fogColor = new THREE.Color(0x7ad8ff);
    this.scene.fog = new THREE.FogExp2(fogColor, 0.0016);

    this.camera = new THREE.PerspectiveCamera(
      62,
      window.innerWidth / window.innerHeight,
      0.1,
      2500
    );
    this.camera.position.copy(this.cameraPos);
    this.camera.rotation.order = 'YXZ';

    this.renderer = new THREE.WebGLRenderer({
      antialias: true,
      powerPreference: 'high-performance'
    });
    this.renderer.setSize(window.innerWidth, window.innerHeight);
    this.renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    this.renderer.toneMapping = THREE.ACESFilmicToneMapping;
    this.renderer.toneMappingExposure = 1.28; // Vibrant saturated exposure
    this.renderer.outputColorSpace = THREE.SRGBColorSpace;
    this.container.appendChild(this.renderer.domElement);

    // Highly saturated vibrant lighting
    this.ambientLight = new THREE.AmbientLight(0xd0f4ff, 1.15);
    this.scene.add(this.ambientLight);

    // Saturated sky blue and emerald ground bounce
    this.hemiLight = new THREE.HemisphereLight(0x38bdf8, 0x44ce1b, 1.18);
    this.scene.add(this.hemiLight);

    // Warm brilliant sunlight
    this.sunLight = new THREE.DirectionalLight(0xfff3c4, 2.6);
    this.sunLight.position.set(160, 200, -220);
    this.scene.add(this.sunLight);
  }

  initTerrain() {
    const width = 850;
    const height = 850;
    const segments = 240;

    const terrainGeo = new THREE.PlaneGeometry(width, height, segments, segments);
    terrainGeo.rotateX(-Math.PI / 2);

    const pos = terrainGeo.attributes.position;
    const count = pos.count;
    const colors = new Float32Array(count * 3);

    // Compute heights and extra saturated vertex color tinting
    for (let i = 0; i < count; i++) {
      const vx = pos.getX(i);
      const vz = pos.getZ(i);
      const vy = getTerrainHeight(vx, vz);
      pos.setY(i, vy);

      // Frutiger Aero extra saturated green gradient:
      // High hill crests get electric sunlit lime, valleys get deep rich emerald
      const heightFactor = THREE.MathUtils.clamp((vy + 15) / 35, 0, 1);
      const r = 0.35 + heightFactor * 0.28;
      const g = 0.88 + heightFactor * 0.12;
      const b = 0.22 + heightFactor * 0.15;

      colors[i * 3] = r;
      colors[i * 3 + 1] = g;
      colors[i * 3 + 2] = b;
    }

    terrainGeo.setAttribute('color', new THREE.BufferAttribute(colors, 3));
    terrainGeo.computeVertexNormals();

    // Grass texture loading
    const textureLoader = new THREE.TextureLoader();
    const grassTex = textureLoader.load(
      '/textures/grass.jpg',
      (texture) => {
        texture.wrapS = THREE.RepeatWrapping;
        texture.wrapT = THREE.RepeatWrapping;
        texture.repeat.set(90, 90);
        texture.colorSpace = THREE.SRGBColorSpace;
        texture.anisotropy = this.renderer.capabilities.getMaxAnisotropy();
        texture.needsUpdate = true;
      }
    );

    this.terrainMat = new THREE.MeshStandardMaterial({
      map: grassTex,
      vertexColors: true,
      roughness: 0.72,
      metalness: 0.02,
      flatShading: false
    });

    this.terrainMesh = new THREE.Mesh(terrainGeo, this.terrainMat);
    this.scene.add(this.terrainMesh);
  }

  initSky() {
    // Saturated Frutiger Aero Skydome
    const skyGeo = new THREE.SphereGeometry(1800, 32, 24);

    const skyShader = {
      vertexShader: `
        varying vec3 vWorldPosition;
        void main() {
          vec4 worldPosition = modelMatrix * vec4(position, 1.0);
          vWorldPosition = worldPosition.xyz;
          gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
        }
      `,
      fragmentShader: `
        varying vec3 vWorldPosition;
        uniform vec3 topColor;
        uniform vec3 bottomColor;
        uniform vec3 sunColor;
        uniform vec3 sunPosition;

        void main() {
          vec3 dir = normalize(vWorldPosition);
          float h = clamp(dir.y * 1.6, 0.0, 1.0);
          
          vec3 sky = mix(bottomColor, topColor, pow(h, 0.55));

          vec3 sunDir = normalize(sunPosition);
          float sunDot = max(dot(dir, sunDir), 0.0);
          float sunGlow = pow(sunDot, 14.0) * 0.9 + pow(sunDot, 90.0) * 1.8;

          vec3 finalColor = sky + sunColor * sunGlow;
          gl_FragColor = vec4(finalColor, 1.0);
        }
      `,
      uniforms: {
        topColor: { value: new THREE.Color(0x0066ee) },    // Deep royal azure
        bottomColor: { value: new THREE.Color(0x8ae4ff) }, // Brilliant luminous cyan
        sunColor: { value: new THREE.Color(0xfff6bd) },    // Warm golden sun
        sunPosition: { value: this.sunLight.position }
      }
    };

    this.skyMat = new THREE.ShaderMaterial({
      vertexShader: skyShader.vertexShader,
      fragmentShader: skyShader.fragmentShader,
      uniforms: skyShader.uniforms,
      side: THREE.BackSide,
      depthWrite: false
    });

    this.skyMesh = new THREE.Mesh(skyGeo, this.skyMat);
    this.scene.add(this.skyMesh);

    // Radiant Sun Glow Sprite
    const sunCanvas = document.createElement('canvas');
    sunCanvas.width = 128;
    sunCanvas.height = 128;
    const ctx = sunCanvas.getContext('2d');
    const grad = ctx.createRadialGradient(64, 64, 0, 64, 64, 64);
    grad.addColorStop(0, 'rgba(255, 255, 255, 1.0)');
    grad.addColorStop(0.2, 'rgba(255, 245, 190, 0.9)');
    grad.addColorStop(0.5, 'rgba(100, 230, 255, 0.35)');
    grad.addColorStop(1, 'rgba(255, 255, 255, 0)');
    ctx.fillStyle = grad;
    ctx.fillRect(0, 0, 128, 128);

    const sunTex = new THREE.CanvasTexture(sunCanvas);
    const sunSpriteMat = new THREE.SpriteMaterial({
      map: sunTex,
      transparent: true,
      blending: THREE.AdditiveBlending,
      depthWrite: false
    });
    this.sunSprite = new THREE.Sprite(sunSpriteMat);
    this.sunSprite.scale.set(180, 180, 1);
    this.sunSprite.position.copy(this.sunLight.position).normalize().multiplyScalar(1500);
    this.scene.add(this.sunSprite);
  }

  initWindTurbines() {
    // Load perfectly cleaned tower and blades textures
    const textureLoader = new THREE.TextureLoader();
    const towerTex = textureLoader.load('/textures/turbine_tower.png');
    const bladesTex = textureLoader.load('/textures/turbine_blades.png');

    towerTex.colorSpace = THREE.SRGBColorSpace;
    bladesTex.colorSpace = THREE.SRGBColorSpace;

    // Tower proportions: original image is 600x841, base y=808, hub (258, 238)
    const towerHeight = 52.0;
    const towerWidth = (600 / 841) * towerHeight; // ~37.1

    // Blades texture is 900x900 square with hub at (450, 450) center
    const bladeSize = (900 / 841) * towerHeight; // ~55.65

    // Hub position relative to ground base
    const hubYOffset = ((808 - 238) / 841) * towerHeight; // ~35.24
    const hubXOffset = ((258 - 300) / 841) * towerHeight; // ~ -2.6

    const towerMat = new THREE.MeshStandardMaterial({
      map: towerTex,
      transparent: true,
      alphaTest: 0.12,
      roughness: 0.3,
      metalness: 0.05,
      side: THREE.DoubleSide
    });

    const bladesMat = new THREE.MeshStandardMaterial({
      map: bladesTex,
      transparent: true,
      alphaTest: 0.12,
      roughness: 0.3,
      metalness: 0.05,
      side: THREE.DoubleSide
    });

    // 4 wind turbines placed together on the distant scenic hill ridge
    const turbineConfigs = [
      { x: -128, z: -180, scale: 0.95, speed: 2.1, angle: -0.28, phase: 0.0 },
      { x: -148, z: -196, scale: 1.05, speed: 1.9, angle: -0.32, phase: 1.4 },
      { x: -168, z: -206, scale: 0.98, speed: 2.2, angle: -0.35, phase: 2.7 },
      { x: -188, z: -192, scale: 1.02, speed: 1.8, angle: -0.30, phase: 4.1 }
    ];

    turbineConfigs.forEach((cfg) => {
      const turbineGroup = new THREE.Group();
      const groundY = getTerrainHeight(cfg.x, cfg.z);

      // 1. Tower Plane (centered vertically so base rests exactly on ground)
      const towerGeo = new THREE.PlaneGeometry(towerWidth, towerHeight);
      const baseShiftY = (towerHeight / 2) - ((841 - 808) / 841 * towerHeight);
      towerGeo.translate(0, baseShiftY, 0);

      const towerMesh = new THREE.Mesh(towerGeo, towerMat);
      turbineGroup.add(towerMesh);

      // 2. Blades Plane (centered on the nacelle hub, clean 1-component rotor)
      const bladesGeo = new THREE.PlaneGeometry(bladeSize, bladeSize);
      const bladesMesh = new THREE.Mesh(bladesGeo, bladesMat);
      bladesMesh.position.set(hubXOffset, hubYOffset, 0.12);
      bladesMesh.rotation.z = cfg.phase;
      turbineGroup.add(bladesMesh);

      // Position the turbine group on the hill
      turbineGroup.position.set(cfg.x, groundY, cfg.z);
      turbineGroup.scale.set(cfg.scale, cfg.scale, cfg.scale);
      turbineGroup.rotation.y = cfg.angle;

      this.scene.add(turbineGroup);

      this.windTurbines.push({
        group: turbineGroup,
        blades: bladesMesh,
        speed: cfg.speed
      });
    });
  }

  initSkylineCity() {
    // Frutiger Aero modern glass skyscraper city in the distance
    const textureLoader = new THREE.TextureLoader();
    const skylineTex = textureLoader.load('/textures/skyline.png');
    skylineTex.colorSpace = THREE.SRGBColorSpace;

    // Aspect ratio: 1024 / 813 = 1.2595
    const cityHeight = 105;
    const cityWidth = cityHeight * (1024 / 813); // ~132.25

    const cityMat = new THREE.MeshStandardMaterial({
      map: skylineTex,
      transparent: true,
      alphaTest: 0.08,
      roughness: 0.35,
      metalness: 0.15,
      side: THREE.DoubleSide
    });

    // Primary city skyline located in the distance at (125, -185)
    const cityGeo = new THREE.PlaneGeometry(cityWidth, cityHeight);
    cityGeo.translate(0, cityHeight / 2 - 2, 0);

    const cityMesh = new THREE.Mesh(cityGeo, cityMat);
    const cityX = 125;
    const cityZ = -185;
    const cityGroundY = getTerrainHeight(cityX, cityZ);

    cityMesh.position.set(cityX, cityGroundY, cityZ);
    cityMesh.rotation.y = Math.atan2(-cityX, -cityZ);

    this.scene.add(cityMesh);

    // Optional secondary distant city layer for atmospheric depth
    const cityGeo2 = new THREE.PlaneGeometry(cityWidth * 0.75, cityHeight * 0.75);
    cityGeo2.translate(0, (cityHeight * 0.75) / 2 - 2, 0);

    const cityMesh2 = new THREE.Mesh(cityGeo2, cityMat);
    const cityX2 = 195;
    const cityZ2 = -145;
    const cityGroundY2 = getTerrainHeight(cityX2, cityZ2);

    cityMesh2.position.set(cityX2, cityGroundY2, cityZ2);
    cityMesh2.rotation.y = Math.atan2(-cityX2, -cityZ2);

    this.scene.add(cityMesh2);
  }

  initControls() {
    const el = this.container;
    const hint = document.getElementById('simple-hint');

    const dismissHint = () => {
      if (!this.hintDismissed && hint) {
        this.hintDismissed = true;
        hint.classList.add('fade-out');
        setTimeout(() => {
          if (hint) hint.style.display = 'none';
        }, 900);
      }
    };

    // Tracking active pointers for single-touch drag and multi-touch pinch zoom
    const activePointers = new Map();
    let initialPinchDist = 0;
    let initialPinchFov = this.camera.fov;

    const getPinchDistance = () => {
      const pts = Array.from(activePointers.values());
      if (pts.length < 2) return 0;
      const dx = pts[0].x - pts[1].x;
      const dy = pts[0].y - pts[1].y;
      return Math.hypot(dx, dy);
    };

    // Pointer events (handles Mouse, Touch, Stylus seamlessly)
    el.addEventListener('pointerdown', (e) => {
      activePointers.set(e.pointerId, { x: e.clientX, y: e.clientY });
      try {
        el.setPointerCapture(e.pointerId);
      } catch (err) {}

      if (activePointers.size === 1) {
        this.isDragging = true;
        this.lastPointerX = e.clientX;
        this.lastPointerY = e.clientY;
      } else if (activePointers.size === 2) {
        this.isDragging = false;
        initialPinchDist = getPinchDistance();
        initialPinchFov = this.camera.fov;
      }
      dismissHint();
    });

    window.addEventListener('pointermove', (e) => {
      if (!activePointers.has(e.pointerId)) return;
      activePointers.set(e.pointerId, { x: e.clientX, y: e.clientY });

      // Two-finger pinch-to-zoom on mobile
      if (activePointers.size >= 2) {
        const currentDist = getPinchDistance();
        if (initialPinchDist > 0 && currentDist > 0) {
          const ratio = initialPinchDist / currentDist;
          this.camera.fov = THREE.MathUtils.clamp(initialPinchFov * ratio, 35, 85);
          this.camera.updateProjectionMatrix();
        }
        dismissHint();
        return;
      }

      // Single-finger / mouse drag to look around
      if (this.isDragging && activePointers.size === 1) {
        const deltaX = e.clientX - this.lastPointerX;
        const deltaY = e.clientY - this.lastPointerY;
        this.lastPointerX = e.clientX;
        this.lastPointerY = e.clientY;

        this.targetYaw -= deltaX * 0.0032;
        this.targetPitch -= deltaY * 0.0032;

        const maxPitch = Math.PI / 2.05;
        this.targetPitch = Math.max(-maxPitch, Math.min(maxPitch, this.targetPitch));
        dismissHint();
      }
    });

    const onPointerUp = (e) => {
      activePointers.delete(e.pointerId);
      try {
        el.releasePointerCapture(e.pointerId);
      } catch (err) {}

      if (activePointers.size === 1) {
        const remaining = Array.from(activePointers.values())[0];
        this.lastPointerX = remaining.x;
        this.lastPointerY = remaining.y;
        this.isDragging = true;
      } else {
        this.isDragging = false;
        initialPinchDist = 0;
      }
    };

    window.addEventListener('pointerup', onPointerUp);
    window.addEventListener('pointercancel', onPointerUp);

    // Direct touch event fallback for mobile browsers
    el.addEventListener('touchstart', (e) => {
      dismissHint();
      if (e.touches.length === 1) {
        this.isDragging = true;
        this.lastPointerX = e.touches[0].clientX;
        this.lastPointerY = e.touches[0].clientY;
      }
    }, { passive: false });

    el.addEventListener('touchmove', (e) => {
      e.preventDefault(); // Prevent native pull-to-refresh / page scrolling
      if (e.touches.length === 1 && this.isDragging) {
        const deltaX = e.touches[0].clientX - this.lastPointerX;
        const deltaY = e.touches[0].clientY - this.lastPointerY;
        this.lastPointerX = e.touches[0].clientX;
        this.lastPointerY = e.touches[0].clientY;

        this.targetYaw -= deltaX * 0.0034;
        this.targetPitch -= deltaY * 0.0034;

        const maxPitch = Math.PI / 2.05;
        this.targetPitch = Math.max(-maxPitch, Math.min(maxPitch, this.targetPitch));
      }
    }, { passive: false });

    el.addEventListener('touchend', () => {
      this.isDragging = false;
    });

    // Zoom via mouse wheel
    window.addEventListener('wheel', (e) => {
      const zoomDelta = e.deltaY * 0.04;
      this.camera.fov = THREE.MathUtils.clamp(this.camera.fov + zoomDelta, 35, 85);
      this.camera.updateProjectionMatrix();
      dismissHint();
    }, { passive: true });
  }

  onWindowResize() {
    this.camera.aspect = window.innerWidth / window.innerHeight;
    this.camera.updateProjectionMatrix();
    this.renderer.setSize(window.innerWidth, window.innerHeight);
  }

  animate() {
    requestAnimationFrame(this.animate);
    const delta = Math.min(this.clock.getDelta(), 0.1);

    // Smooth damping for camera look rotation
    this.yaw = THREE.MathUtils.lerp(this.yaw, this.targetYaw, 0.14);
    this.pitch = THREE.MathUtils.lerp(this.pitch, this.targetPitch, 0.14);

    this.camera.rotation.y = this.yaw;
    this.camera.rotation.x = this.pitch;

    // Animate all 4 wind turbines spinning smoothly around their hubs
    for (let i = 0; i < this.windTurbines.length; i++) {
      const turbine = this.windTurbines[i];
      turbine.blades.rotation.z += turbine.speed * delta;
    }

    this.renderer.render(this.scene, this.camera);
  }
}

// Start application
window.addEventListener('DOMContentLoaded', () => {
  new WorldExplorer();
});
```

---

## 5. Asset Generation & Python Image Pipelines

If reproducing the assets from raw images, run the following Python scripts (requires `Pillow`, `numpy`, `scipy`):

### Separating Wind Turbine Blades & Tower
```python
from PIL import Image
import numpy as np
import scipy.ndimage as ndi

# Load original transparent wind turbine PNG (600x841)
orig = Image.open('turbine_raw.png').convert('RGBA')
arr = np.array(orig)
h, w, _ = arr.shape
hub_x, hub_y = 258, 238

# --- 1. EXTRACT BLADES ---
blades_arr = np.array(arr)
# Mask below y=585 (where blades end and tower begins)
blades_arr[585:, :, 3] = 0

# Mask out tower column between y=245 and 585
for y in range(245, 585):
    t = (y - 245) / (585 - 245)
    tower_left = int(round(245 * (1 - t) + 248 * t))
    blades_arr[y, tower_left:, 3] = 0

for y in range(238, 260):
    blades_arr[y, 275:, 3] = 0

# Center hub onto a 900x900 square canvas
max_radius = 450
sq_size = max_radius * 2
sq_blades = Image.new('RGBA', (sq_size, sq_size), (0, 0, 0, 0))
blades_img = Image.fromarray(blades_arr)
sq_blades.paste(blades_img, (max_radius - hub_x, max_radius - hub_y), blades_img)

# Isolate ONLY the primary connected component (eliminating stray tower fragments)
b_alpha = np.array(sq_blades)[:, :, 3]
lbl, num = ndi.label(b_alpha > 10)
sizes = [np.sum(lbl == i) for i in range(1, num + 1)]
main_label = np.argmax(sizes) + 1

clean_blades_arr = np.zeros_like(np.array(sq_blades))
mask = (lbl == main_label)
clean_blades_arr[mask] = np.array(sq_blades)[mask]
Image.fromarray(clean_blades_arr).save('public/textures/turbine_blades.png')

# --- 2. EXTRACT TOWER ---
tower_arr = np.array(arr)
for y in range(0, 235):
    tower_arr[y, :, 3] = 0
for y in range(235, h):
    for x in range(0, 246):
        tower_arr[y, x, 3] = 0
for y in range(235, h):
    for x in range(285, w):
        tower_arr[y, x, 3] = 0

Image.fromarray(tower_arr).save('public/textures/turbine_tower.png')
```

### City Skyline Alpha Ground Blend
```python
from PIL import Image
import numpy as np

im = Image.open('skyline_raw.png').convert('RGBA')
arr = np.array(im)
h, w, _ = arr.shape

# Soft alpha fade on bottom 18 pixels for seamless grass hill embedding
for y in range(h - 18, h):
    fade = (h - 1 - y) / 18.0
    arr[y, :, 3] = (arr[y, :, 3].astype(float) * fade).astype(np.uint8)

Image.fromarray(arr).save('public/textures/skyline.png')
```

---

## 6. Core Mathematical Formulations

### Rolling Hills Heightmap
```javascript
export function getTerrainHeight(x, z) {
  const h1 = Math.sin(x * 0.011) * Math.cos(z * 0.011) * 11.0;
  const h2 = Math.sin(x * 0.022 + 1.4) * Math.cos(z * 0.019 + 0.6) * 5.0;
  const h3 = Math.cos((x - z) * 0.007) * 8.5;
  const h4 = Math.sin(x * 0.04) * Math.cos(z * 0.04) * 1.8;
  return h1 + h2 + h3 + h4;
}
```

### Camera Look-Around & Mobile Touch Controls
- **Interpolation**: `yaw = THREE.MathUtils.lerp(yaw, targetYaw, 0.14)` and `pitch = THREE.MathUtils.lerp(pitch, targetPitch, 0.14)`.
- **Clamping**: `pitch` clamped to `[-Math.PI / 2.05, Math.PI / 2.05]`.
- **Touch Tracking**: `activePointers` Map with `setPointerCapture` and fallback `touchmove` listeners preventing default pull-to-refresh.
- **Pinch-to-Zoom**: Two-finger Euclidean distance calculation modulating camera `fov` between $35^\circ$ and $85^\circ$.

### Turbine Rotation & Anchor Placement
- **Ground Alignment**: `baseShiftY = (towerHeight / 2) - ((841 - 808) / 841 * towerHeight)`.
- **Hub Offset**: `hubYOffset = ((808 - 238) / 841) * towerHeight`, `hubXOffset = ((258 - 300) / 841) * towerHeight`.
- **Animation**: `blades.rotation.z += turbine.speed * delta`.

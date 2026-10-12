# 📱 FRUTIGER AERO WORLD EXPLORER — META POCKET PROMPT

> **PROMPT INSTRUCTION FOR META POCKET AI**:  
> You are building a **Frutiger Aero World Explorer** mobile gizmo in Three.js as a **single, self-contained HTML/JS app**.  
> **Images are attached directly to this prompt**:
> - **Image 1**: Lush green grass texture (repeat-tiled across the rolling hills).
> - **Image 2**: Wind turbine (towers placed on distant hill ridge, rotor blades spinning around hub).
> - **Image 3**: City skyline (distant modern glass skyscrapers rising over the horizon).
>
> **CRITICAL**: Do **NOT** generate an npm project or file system. Do **NOT** generate placeholder images. Output a **single, fully working HTML file** using CDN Three.js (`https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js`) that uses the attached images.

---

## 🎨 Aesthetic & Experience
- **Theme**: Saturated 2000s **Frutiger Aero** (vibrant Windows XP Bliss rolling green hills, brilliant royal azure to cyan sky, radiant golden sun glow).
- **Camera**: First-person panoramic look-around standing atop a gentle green hill overlooking the landscape.
- **Distant Landmarks**:
  1. A cluster of **4 wind turbines** grouped together on a distant hill ridge, with their blades continuously spinning at realistic speeds.
  2. A **futuristic glass skyscraper metropolis** on the distant horizon behind the hills.
- **Controls**:
  - **Single finger touch drag**: Look around 360° smoothly across the hills and sky.
  - **Two-finger pinch**: Zoom in / out (adjust field of view).
  - Works on mobile touchscreens and desktop mouse.

---

## 💻 Complete Single-File Code

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>Frutiger Aero World Explorer</title>
  <style>
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
      font-family: system-ui, -apple-system, sans-serif;
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
      filter: saturate(1.38) contrast(1.06);
    }
    #canvas-container:active {
      cursor: grabbing;
    }
    .hint-pill {
      position: absolute;
      bottom: 24px;
      left: 50%;
      transform: translateX(-50%);
      z-index: 10;
      pointer-events: none;
      background: rgba(255, 255, 255, 0.6);
      backdrop-filter: blur(14px);
      -webkit-backdrop-filter: blur(14px);
      border: 1px solid rgba(255, 255, 255, 0.85);
      padding: 7px 18px;
      border-radius: 999px;
      color: #01436e;
      font-size: 0.82rem;
      font-weight: 600;
      box-shadow: 0 6px 20px rgba(0, 80, 160, 0.18);
      transition: opacity 0.8s ease;
    }
    .hint-pill.hidden {
      opacity: 0;
    }
  </style>
</head>
<body>
  <div id="canvas-container"></div>
  <div id="hint" class="hint-pill">✦ Drag to look around &bull; Pinch to zoom</div>

  <!-- Three.js CDN -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>

  <script>
    // --- 1. ROLLING HILLS MATHEMATICAL HEIGHTMAP ---
    function getTerrainHeight(x, z) {
      const h1 = Math.sin(x * 0.011) * Math.cos(z * 0.011) * 11.0;
      const h2 = Math.sin(x * 0.022 + 1.4) * Math.cos(z * 0.019 + 0.6) * 5.0;
      const h3 = Math.cos((x - z) * 0.007) * 8.5;
      const h4 = Math.sin(x * 0.04) * Math.cos(z * 0.04) * 1.8;
      return h1 + h2 + h3 + h4;
    }

    // --- 2. THREE.JS SCENE SETUP ---
    const container = document.getElementById('canvas-container');
    const scene = new THREE.Scene();
    scene.fog = new THREE.FogExp2(0x7ad8ff, 0.0016);

    const camera = new THREE.PerspectiveCamera(62, window.innerWidth / window.innerHeight, 0.1, 2500);
    const camY = getTerrainHeight(0, 0) + 2.2;
    camera.position.set(0, camY, 0);
    camera.rotation.order = 'YXZ';

    const renderer = new THREE.WebGLRenderer({ antialias: true, powerPreference: 'high-performance' });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    renderer.outputEncoding = THREE.sRGBEncoding;
    renderer.toneMapping = THREE.ACESFilmicToneMapping;
    renderer.toneMappingExposure = 1.28;
    container.appendChild(renderer.domElement);

    // Highly saturated vibrant lighting
    scene.add(new THREE.AmbientLight(0xd0f4ff, 1.15));
    scene.add(new THREE.HemisphereLight(0x38bdf8, 0x44ce1b, 1.18));
    const sunLight = new THREE.DirectionalLight(0xfff3c4, 2.6);
    sunLight.position.set(160, 200, -220);
    scene.add(sunLight);

    // --- 3. SATURATED SKY DOME SHADER ---
    const skyGeo = new THREE.SphereGeometry(1800, 32, 24);
    const skyMat = new THREE.ShaderMaterial({
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
          gl_FragColor = vec4(sky + sunColor * sunGlow, 1.0);
        }
      `,
      uniforms: {
        topColor: { value: new THREE.Color(0x0066ee) },
        bottomColor: { value: new THREE.Color(0x8ae4ff) },
        sunColor: { value: new THREE.Color(0xfff6bd) },
        sunPosition: { value: sunLight.position }
      },
      side: THREE.BackSide,
      depthWrite: false
    });
    scene.add(new THREE.Mesh(skyGeo, skyMat));

    // Radiant Sun Glow Sprite
    const sunCanvas = document.createElement('canvas');
    sunCanvas.width = 128; sunCanvas.height = 128;
    const sCtx = sunCanvas.getContext('2d');
    const sGrad = sCtx.createRadialGradient(64, 64, 0, 64, 64, 64);
    sGrad.addColorStop(0, 'rgba(255, 255, 255, 1.0)');
    sGrad.addColorStop(0.25, 'rgba(255, 245, 190, 0.9)');
    sGrad.addColorStop(0.5, 'rgba(100, 230, 255, 0.35)');
    sGrad.addColorStop(1, 'rgba(255, 255, 255, 0)');
    sCtx.fillStyle = sGrad;
    sCtx.fillRect(0, 0, 128, 128);
    const sunSprite = new THREE.Sprite(new THREE.SpriteMaterial({
      map: new THREE.CanvasTexture(sunCanvas),
      transparent: true,
      blending: THREE.AdditiveBlending,
      depthWrite: false
    }));
    sunSprite.scale.set(180, 180, 1);
    sunSprite.position.copy(sunLight.position).normalize().multiplyScalar(1500);
    scene.add(sunSprite);

    // --- 4. ROLLING GREEN HILLS TERRAIN ---
    const terrainGeo = new THREE.PlaneGeometry(850, 850, 240, 240);
    terrainGeo.rotateX(-Math.PI / 2);
    const pos = terrainGeo.attributes.position;
    const colors = new Float32Array(pos.count * 3);

    for (let i = 0; i < pos.count; i++) {
      const vx = pos.getX(i);
      const vz = pos.getZ(i);
      const vy = getTerrainHeight(vx, vz);
      pos.setY(i, vy);
      const heightFactor = THREE.MathUtils.clamp((vy + 15) / 35, 0, 1);
      colors[i * 3] = 0.35 + heightFactor * 0.28;
      colors[i * 3 + 1] = 0.88 + heightFactor * 0.12;
      colors[i * 3 + 2] = 0.22 + heightFactor * 0.15;
    }
    terrainGeo.setAttribute('color', new THREE.BufferAttribute(colors, 3));
    terrainGeo.computeVertexNormals();

    // --- 4. ASSET BINDINGS (Replace with attached image URLs or Base64 in Meta Pocket) ---
    const GRASS_SRC = 'grass.jpg';       // Attached Image 1
    const TURBINE_SRC = 'turbine.png';   // Attached Image 2 (Single wind turbine photo)
    const SKYLINE_SRC = 'skyline.png';   // Attached Image 3 (Skyline photo)

    const textureLoader = new THREE.TextureLoader();
    
    // Terrain Grass Texture
    const grassTex = textureLoader.load(GRASS_SRC, (tex) => {
      tex.wrapS = THREE.RepeatWrapping;
      tex.wrapT = THREE.RepeatWrapping;
      tex.repeat.set(90, 90);
      tex.encoding = THREE.sRGBEncoding;
      tex.anisotropy = renderer.capabilities.getMaxAnisotropy();
    });

    const terrainMesh = new THREE.Mesh(terrainGeo, new THREE.MeshStandardMaterial({
      map: grassTex,
      vertexColors: true,
      roughness: 0.72,
      metalness: 0.02
    }));
    scene.add(terrainMesh);

    // --- 5. 4 ANIMATED WIND TURBINES (Separated dynamically at runtime) ---
    const towerHeight = 52.0;
    const towerWidth = (600 / 841) * towerHeight;
    const bladeSize = (900 / 841) * towerHeight;
    const hubYOffset = ((808 - 238) / 841) * towerHeight;
    const hubXOffset = ((258 - 300) / 841) * towerHeight;

    const towerMat = new THREE.MeshStandardMaterial({ transparent: true, alphaTest: 0.12, side: THREE.DoubleSide });
    const bladesMat = new THREE.MeshStandardMaterial({ transparent: true, alphaTest: 0.12, side: THREE.DoubleSide });

    // Load single wind turbine image and dynamically split into tower & blades via HTML5 Canvas
    const rawTurbineImg = new Image();
    rawTurbineImg.crossOrigin = 'anonymous';
    rawTurbineImg.onload = () => {
      // 1. Tower Canvas
      const tCanvas = document.createElement('canvas');
      tCanvas.width = 600; tCanvas.height = 841;
      const tCtx = tCanvas.getContext('2d');
      tCtx.drawImage(rawTurbineImg, 0, 0);
      tCtx.clearRect(0, 0, 600, 235);
      tCtx.clearRect(0, 235, 246, 606);
      tCtx.clearRect(285, 235, 315, 606);
      towerMat.map = new THREE.CanvasTexture(tCanvas);
      towerMat.needsUpdate = true;

      // 2. Blades Canvas (centered at hub 258, 238)
      const bCanvas = document.createElement('canvas');
      bCanvas.width = 900; bCanvas.height = 900;
      const bCtx = bCanvas.getContext('2d');
      bCtx.drawImage(rawTurbineImg, 192, 212);
      bCtx.clearRect(430, 457, 500, 500);
      bCtx.clearRect(0, 780, 900, 150);
      bladesMat.map = new THREE.CanvasTexture(bCanvas);
      bladesMat.needsUpdate = true;
    };
    rawTurbineImg.src = TURBINE_SRC;

    const windTurbines = [];
    const turbineConfigs = [
      { x: -128, z: -180, scale: 0.95, speed: 2.1, angle: -0.28, phase: 0.0 },
      { x: -148, z: -196, scale: 1.05, speed: 1.9, angle: -0.32, phase: 1.4 },
      { x: -168, z: -206, scale: 0.98, speed: 2.2, angle: -0.35, phase: 2.7 },
      { x: -188, z: -192, scale: 1.02, speed: 1.8, angle: -0.30, phase: 4.1 }
    ];

    turbineConfigs.forEach((cfg) => {
      const group = new THREE.Group();
      const groundY = getTerrainHeight(cfg.x, cfg.z);

      const towerGeo = new THREE.PlaneGeometry(towerWidth, towerHeight);
      towerGeo.translate(0, (towerHeight / 2) - ((841 - 808) / 841 * towerHeight), 0);
      group.add(new THREE.Mesh(towerGeo, towerMat));

      const bladesGeo = new THREE.PlaneGeometry(bladeSize, bladeSize);
      const bladesMesh = new THREE.Mesh(bladesGeo, bladesMat);
      bladesMesh.position.set(hubXOffset, hubYOffset, 0.12);
      bladesMesh.rotation.z = cfg.phase;
      group.add(bladesMesh);

      group.position.set(cfg.x, groundY, cfg.z);
      group.scale.set(cfg.scale, cfg.scale, cfg.scale);
      group.rotation.y = cfg.angle;
      scene.add(group);

      windTurbines.push({ group, blades: bladesMesh, speed: cfg.speed });
    });

    // --- 6. DISTANT MODERN CITY SKYLINE ---
    const skylineTex = textureLoader.load(SKYLINE_SRC);
    skylineTex.encoding = THREE.sRGBEncoding;

    const cityHeight = 105;
    const cityWidth = cityHeight * (1024 / 813);
    const cityMat = new THREE.MeshStandardMaterial({ map: skylineTex, transparent: true, alphaTest: 0.08, side: THREE.DoubleSide });

    const cityGeo = new THREE.PlaneGeometry(cityWidth, cityHeight);
    cityGeo.translate(0, cityHeight / 2 - 2, 0);

    const cityMesh = new THREE.Mesh(cityGeo, cityMat);
    const cityX = 125, cityZ = -185;
    cityMesh.position.set(cityX, getTerrainHeight(cityX, cityZ), cityZ);
    cityMesh.rotation.y = Math.atan2(-cityX, -cityZ);
    scene.add(cityMesh);

    // --- 7. MOBILE TOUCH & DESKTOP LOOK-AROUND CONTROLS ---
    let yaw = 0.0, pitch = 0.02, targetYaw = 0.0, targetPitch = 0.02;
    let isDragging = false, lastPointerX = 0, lastPointerY = 0;
    const activePointers = new Map();
    let initialPinchDist = 0, initialPinchFov = camera.fov;

    const hint = document.getElementById('hint');
    const dismissHint = () => { if (hint) { hint.classList.add('hidden'); } };

    const getPinchDist = () => {
      const pts = Array.from(activePointers.values());
      return pts.length >= 2 ? Math.hypot(pts[0].x - pts[1].x, pts[0].y - pts[1].y) : 0;
    };

    container.addEventListener('pointerdown', (e) => {
      activePointers.set(e.pointerId, { x: e.clientX, y: e.clientY });
      try { container.setPointerCapture(e.pointerId); } catch (_) {}
      if (activePointers.size === 1) {
        isDragging = true;
        lastPointerX = e.clientX; lastPointerY = e.clientY;
      } else if (activePointers.size === 2) {
        isDragging = false;
        initialPinchDist = getPinchDist();
        initialPinchFov = camera.fov;
      }
      dismissHint();
    });

    window.addEventListener('pointermove', (e) => {
      if (!activePointers.has(e.pointerId)) return;
      activePointers.set(e.pointerId, { x: e.clientX, y: e.clientY });

      if (activePointers.size >= 2) {
        const d = getPinchDist();
        if (initialPinchDist > 0 && d > 0) {
          camera.fov = THREE.MathUtils.clamp(initialPinchFov * (initialPinchDist / d), 35, 85);
          camera.updateProjectionMatrix();
        }
        return;
      }

      if (isDragging && activePointers.size === 1) {
        const dx = e.clientX - lastPointerX;
        const dy = e.clientY - lastPointerY;
        lastPointerX = e.clientX; lastPointerY = e.clientY;
        targetYaw -= dx * 0.0032;
        targetPitch -= dy * 0.0032;
        targetPitch = Math.max(-Math.PI / 2.05, Math.min(Math.PI / 2.05, targetPitch));
      }
    });

    const onPointerUp = (e) => {
      activePointers.delete(e.pointerId);
      try { container.releasePointerCapture(e.pointerId); } catch (_) {}
      if (activePointers.size === 1) {
        const remaining = Array.from(activePointers.values())[0];
        lastPointerX = remaining.x; lastPointerY = remaining.y;
        isDragging = true;
      } else {
        isDragging = false;
        initialPinchDist = 0;
      }
    };
    window.addEventListener('pointerup', onPointerUp);
    window.addEventListener('pointercancel', onPointerUp);

    // Fallback touch listeners for mobile Safari / Android
    container.addEventListener('touchmove', (e) => e.preventDefault(), { passive: false });

    // Wheel zoom
    window.addEventListener('wheel', (e) => {
      camera.fov = THREE.MathUtils.clamp(camera.fov + e.deltaY * 0.04, 35, 85);
      camera.updateProjectionMatrix();
    }, { passive: true });

    // Responsive resize
    window.addEventListener('resize', () => {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    });

    // --- 8. ANIMATION LOOP ---
    const clock = new THREE.Clock();
    function animate() {
      requestAnimationFrame(animate);
      const delta = Math.min(clock.getDelta(), 0.1);

      // Smooth damping look rotation
      yaw = THREE.MathUtils.lerp(yaw, targetYaw, 0.14);
      pitch = THREE.MathUtils.lerp(pitch, targetPitch, 0.14);
      camera.rotation.y = yaw;
      camera.rotation.x = pitch;

      // Spin wind turbine blades
      for (let i = 0; i < windTurbines.length; i++) {
        windTurbines[i].blades.rotation.z += windTurbines[i].speed * delta;
      }

      renderer.render(scene, camera);
    }
    animate();
  </script>
</body>
</html>
```

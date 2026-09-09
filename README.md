# chiraphut27-prog.github.io
<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TECHNOLOGY INTERNET - Three.js 3D Globe</title>
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Rajdhani:wght@300;500;700&display=swap" rel="stylesheet">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body, html {
            width: 100%;
            height: 100%;
            overflow: hidden;
            background-color: #020718;
            font-family: 'Rajdhani', sans-serif;
            color: #ffffff;
        }

        #canvas-container {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 1;
        }

        /* Overlay UI Styles */
        .ui-layer {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 2;
            pointer-events: none;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            padding: 40px 60px;
        }

        .header-content {
            margin-top: 20px;
            max-width: 650px;
            pointer-events: auto;
        }

        .title-crosshair {
            position: relative;
            padding-left: 20px;
            border-left: 2px solid rgba(0, 210, 255, 0.6);
        }

        .title-crosshair::before {
            content: '';
            position: absolute;
            left: -6px;
            top: 0;
            width: 10px;
            height: 2px;
            background-color: #00f0ff;
        }

        h1.main-title {
            font-family: 'Orbitron', sans-serif;
            font-size: 3.5rem;
            font-weight: 900;
            line-height: 1.1;
            letter-spacing: 4px;
            color: #ffffff;
            text-shadow: 0 0 20px rgba(0, 180, 255, 0.6);
        }

        h2.sub-title {
            font-family: 'Orbitron', sans-serif;
            font-size: 3.2rem;
            font-weight: 700;
            line-height: 1.1;
            letter-spacing: 6px;
            color: #4facfe;
            text-shadow: 0 0 15px rgba(79, 172, 254, 0.8);
            margin-bottom: 25px;
        }

        .tagline {
            font-size: 1.2rem;
            letter-spacing: 2px;
            color: #8ab4f8;
            opacity: 0.8;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .tagline::before {
            content: '';
            display: inline-block;
            width: 30px;
            height: 1px;
            background: #00f0ff;
        }

        /* Sci-Fi Grid Line UI Decorative elements */
        .decor-cross {
            position: absolute;
            font-family: monospace;
            color: rgba(0, 240, 255, 0.4);
            font-size: 18px;
            user-select: none;
        }

        .decor-top-right {
            top: 40px;
            right: 60px;
            border-right: 2px solid #00f0ff;
            border-top: 2px solid #00f0ff;
            width: 40px;
            height: 40px;
        }

        .decor-bottom-left {
            bottom: 40px;
            left: 60px;
            border-left: 2px solid #00f0ff;
            border-bottom: 2px solid #00f0ff;
            width: 40px;
            height: 40px;
        }

        /* Responsive UI */
        @media (max-width: 768px) {
            .ui-layer {
                padding: 20px;
            }
            h1.main-title {
                font-size: 2.2rem;
            }
            h2.sub-title {
                font-size: 2rem;
            }
        }
    </style>
</head>
<body>

    <div id="canvas-container"></div>

    <!-- UI Overlay matching the design in the image -->
    <div class="ui-layer">
        <div class="header-content">
            <div class="title-crosshair">
                <h1 class="main-title">TECHNOLOGY</h1>
                <h2 class="sub-title">INTERNET</h2>
            </div>
            <p class="tagline">Here is where your presentation begins</p>
        </div>

        <div class="decor-top-right"></div>
        <div class="decor-bottom-left"></div>
    </div>

    <!-- Three.js and OrbitControls via CDN -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js"></script>

    <script>
        // 1. Scene, Camera, Renderer Setup
        const container = document.getElementById('canvas-container');
        const scene = new THREE.Scene();
        scene.fog = new THREE.FogExp2(0x020718, 0.0015);

        const camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000);
        
        // Offset camera slightly to position globe on the right side (like the image)
        camera.position.set(2, 1, 7);

        const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
        renderer.toneMapping = THREE.ACESFilmicToneMapping;
        container.appendChild(renderer.domElement);

        // Orbit Controls
        const controls = new THREE.OrbitControls(camera, renderer.domElement);
        controls.enableDamping = true;
        controls.dampingFactor = 0.05;
        controls.rotateSpeed = 0.8;
        controls.enablePan = false;
        controls.minDistance = 4;
        controls.maxDistance = 12;

        // 2. Globe Group Setup
        const globeGroup = new THREE.Group();
        // Move Globe to the right side of the screen
        globeGroup.position.set(1.8, 0, 0);
        scene.add(globeGroup);

        const globeRadius = 2;

        // --- Core Inner Globe (Solid Base with Blue Glow) ---
        const sphereGeo = new THREE.SphereGeometry(globeRadius, 64, 64);
        const sphereMat = new THREE.MeshPhongMaterial({
            color: 0x051f42,
            emissive: 0x021028,
            shininess: 25,
            transparent: true,
            opacity: 0.85,
            wireframe: false
        });
        const innerGlobe = new THREE.Mesh(sphereGeo, sphereMat);
        globeGroup.add(innerGlobe);

        // --- Tech Wireframe Outer Mesh ---
        const wireframeGeo = new THREE.SphereGeometry(globeRadius + 0.02, 36, 36);
        const wireframeMat = new THREE.MeshBasicMaterial({
            color: 0x00d2ff,
            wireframe: true,
            transparent: true,
            opacity: 0.15
        });
        const wireframeGlobe = new THREE.Mesh(wireframeGeo, wireframeMat);
        globeGroup.add(wireframeGlobe);

        // --- Outer Digital Atmosphere Glow ---
        const atmosphereGeo = new THREE.SphereGeometry(globeRadius + 0.35, 64, 64);
        const atmosphereMat = new THREE.ShaderMaterial({
            vertexShader: `
                varying vec3 vNormal;
                void main() {
                    vNormal = normalize(normalMatrix * normal);
                    gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
                }
            `,
            fragmentShader: `
                varying vec3 vNormal;
                void main() {
                    float intensity = pow(0.6 - dot(vNormal, vec3(0, 0, 1.0)), 2.0);
                    gl_FragColor = vec4(0.0, 0.75, 1.0, 1.0) * intensity;
                }
            `,
            blending: THREE.AdditiveBlending,
            side: THREE.BackSide,
            transparent: true
        });
        const atmosphere = new THREE.Mesh(atmosphereGeo, atmosphereMat);
        globeGroup.add(atmosphere);

        // --- Dot Matrix / Tech Points for Continents & Grid ---
        const particleCount = 2500;
        const particleGeo = new THREE.BufferGeometry();
        const positions = new Float32Array(particleCount * 3);

        for (let i = 0; i < particleCount; i++) {
            const phi = Math.acos(-1 + (2 * i) / particleCount);
            const theta = Math.sqrt(particleCount * Math.PI) * phi;

            const r = globeRadius + 0.03;
            positions[i * 3] = r * Math.cos(theta) * Math.sin(phi);
            positions[i * 3 + 1] = r * Math.sin(theta) * Math.sin(phi);
            positions[i * 3 + 2] = r * Math.cos(phi);
        }

        particleGeo.setAttribute('position', new THREE.BufferAttribute(positions, 3));
        const particleMat = new THREE.PointsMaterial({
            color: 0x00f0ff,
            size: 0.035,
            transparent: true,
            opacity: 0.8,
            blending: THREE.AdditiveBlending
        });
        const pointCloud = new THREE.Points(particleGeo, particleMat);
        globeGroup.add(pointCloud);

        // --- Orbital Rings (Equatorial & Tilted Tech Rings) ---
        function createRing(radius, color, rotationX = 0, rotationY = 0) {
            const ringGeo = new THREE.RingGeometry(radius, radius + 0.015, 128);
            const ringMat = new THREE.MeshBasicMaterial({
                color: color,
                side: THREE.DoubleSide,
                transparent: true,
                opacity: 0.5,
                blending: THREE.AdditiveBlending
            });
            const ring = new THREE.Mesh(ringGeo, ringMat);
            ring.rotation.x = rotationX;
            ring.rotation.y = rotationY;
            return ring;
        }

        const ring1 = createRing(globeRadius + 0.5, 0x00f0ff, Math.PI / 2.5, 0.2);
        const ring2 = createRing(globeRadius + 0.8, 0x0077ff, Math.PI / 3, -0.4);
        globeGroup.add(ring1);
        globeGroup.add(ring2);

        // --- Background Floating Digital Particles ---
        const bgParticleCount = 1000;
        const bgParticleGeo = new THREE.BufferGeometry();
        const bgPositions = new Float32Array(bgParticleCount * 3);

        for (let i = 0; i < bgParticleCount * 3; i += 3) {
            bgPositions[i] = (Math.random() - 0.5) * 20;
            bgPositions[i + 1] = (Math.random() - 0.5) * 20;
            bgPositions[i + 2] = (Math.random() - 0.5) * 20;
        }

        bgParticleGeo.setAttribute('position', new THREE.BufferAttribute(bgPositions, 3));
        const bgParticleMat = new THREE.PointsMaterial({
            color: 0x3399ff,
            size: 0.02,
            transparent: true,
            opacity: 0.5
        });
        const bgParticles = new THREE.Points(bgParticleGeo, bgParticleMat);
        scene.add(bgParticles);

        // 3. Lighting Setup
        const ambientLight = new THREE.AmbientLight(0x021028, 2);
        scene.add(ambientLight);

        const mainLight = new THREE.DirectionalLight(0x00f0ff, 2.5);
        mainLight.position.set(5, 3, 5);
        scene.add(mainLight);

        const blueRimLight = new THREE.PointLight(0x0055ff, 4, 10);
        blueRimLight.position.set(-5, -2, -2);
        scene.add(blueRimLight);

        // 4. Animation Loop
        const clock = new THREE.Clock();

        function animate() {
            requestAnimationFrame(animate);

            const elapsedTime = clock.getElapsedTime();

            // Rotate Globe & Components
            innerGlobe.rotation.y = elapsedTime * 0.08;
            wireframeGlobe.rotation.y = elapsedTime * 0.08;
            pointCloud.rotation.y = elapsedTime * 0.1;
            
            // Rotate orbital rings at different speeds
            ring1.rotation.z = elapsedTime * 0.05;
            ring2.rotation.z = -elapsedTime * 0.03;

            // Slowly rotate background particles
            bgParticles.rotation.y = elapsedTime * 0.02;

            controls.update();
            renderer.render(scene, camera);
        }

        animate();

        // 5. Window Resize Handler
        window.addEventListener('resize', onWindowResize, false);

        function onWindowResize() {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();

            renderer.setSize(window.innerWidth, window.innerHeight);

            // Responsive Globe Position
            if (window.innerWidth < 768) {
                globeGroup.position.set(0, -1, 0); // Center lower on mobile
            } else {
                globeGroup.position.set(1.8, 0, 0); // Right side on desktop
            }
        }

        // Initial responsive check
        onWindowResize();

    </script>
</body>
</html>

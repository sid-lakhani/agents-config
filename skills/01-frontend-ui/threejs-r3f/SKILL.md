---
name: threejs-r3f
description: Production-grade Three.js and React Three Fiber (R3F) 3D web development. Use when building interactive 3D web experiences, 3D product showcases, hero canvases, custom GLSL shaders, 3D model loaders (GLTF/GLB), particle systems, and WebGL animations.
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
metadata:
  version: "2.0.0"
  stack: "threejs, r3f, drei, postprocessing"
---

# Three.js & React Three Fiber (R3F) Master Skill

Build high-performance, cinematic 3D web experiences for modern React and Next.js applications using Three.js (r170+), `@react-three/fiber` (R3F), and `@react-three/drei`.

---

## 1. Core Stack & Dependencies

```bash
npm install three @types/three @react-three/fiber @react-three/drei @react-three/postprocessing
# Optional for GLTF model compression:
npm install three-stdlib
```

---

## 2. Production Canvas Architecture

Always isolate Canvas into a client component with DPR clamping, suspense boundaries, and proper responsive sizing.

```tsx
// components/canvas/SceneCanvas.tsx
"use client";

import { Canvas } from "@react-three/fiber";
import { Suspense } from "react";
import { Loader } from "@react-three/drei";

interface SceneCanvasProps {
  children: React.ReactNode;
  className?: string;
}

export function SceneCanvas({ children, className = "w-full h-full" }: SceneCanvasProps) {
  return (
    <div className={`relative ${className}`}>
      <Canvas
        camera={{ position: [0, 0, 5], fov: 45, near: 0.1, far: 1000 }}
        dpr={[1, 2]} // CRITICAL: Clamp DPR between 1 and 2 to protect mobile GPU/battery
        gl={{
          antialias: true,
          alpha: true,
          powerPreference: "high-performance",
          preserveDrawingBuffer: false,
        }}
        onCreated={({ gl }) => {
          gl.toneMappingExposure = 1.2;
        }}
      >
        <Suspense fallback={null}>
          {children}
        </Suspense>
      </Canvas>
      <Loader />
    </div>
  );
}
```

---

## 3. Interactive 3D Product Showcase (Floating & Controls)

Use smooth damped movement, environment lighting, and contact shadows.

```tsx
// components/canvas/ProductExperience.tsx
"use client";

import { useRef } from "react";
import { useFrame } from "@react-three/fiber";
import { Float, PresentationControls, ContactShadows, Environment, MeshDistortMaterial } from "@react-three/drei";
import * as THREE from "three";

export function ProductExperience() {
  const meshRef = useRef<THREE.Mesh>(null!);

  // Smooth frame loop animation
  useFrame((state, delta) => {
    meshRef.current.rotation.y += delta * 0.2;
  });

  return (
    <>
      <ambientLight intensity={0.7} />
      <directionalLight position={[10, 10, 5]} intensity={1.5} castShadow />

      {/* Constrained mouse-based rotation */}
      <PresentationControls
        global={false}
        cursor={true}
        speed={1.5}
        zoom={1}
        polar={[-Math.PI / 6, Math.PI / 6]}
        azimuth={[-Math.PI / 4, Math.PI / 4]}
      >
        <Float speed={2} rotationIntensity={0.5} floatIntensity={1}>
          <mesh ref={meshRef} castShadow receiveShadow>
            <sphereGeometry args={[1.2, 64, 64]} />
            <MeshDistortMaterial
              color="#4f46e5"
              attach="material"
              distort={0.3}
              speed={1.5}
              roughness={0.2}
              metalness={0.8}
            />
          </mesh>
        </Float>
      </PresentationControls>

      <ContactShadows
        position={[0, -1.8, 0]}
        opacity={0.6}
        scale={10}
        blur={2.5}
        far={4}
      />
      <Environment preset="city" />
    </>
  );
}
```

---

## 4. Production GLTF / GLB Model Loading

Always preload models and apply Draco compression when available.

```tsx
// components/canvas/ModelViewer.tsx
"use client";

import { useGLTF } from "@react-three/drei";
import { useRef, useEffect } from "react";
import * as THREE from "three";

interface ModelViewerProps {
  url: string;
}

export function ModelViewer({ url }: ModelViewerProps) {
  const { scene, animations } = useGLTF(url);
  const groupRef = useRef<THREE.Group>(null);

  useEffect(() => {
    // Enable shadows on all child meshes
    scene.traverse((child) => {
      if ((child as THREE.Mesh).isMesh) {
        child.castShadow = true;
        child.receiveShadow = true;
      }
    });

    // Cleanup resources on unmount
    return () => {
      scene.traverse((child) => {
        if ((child as THREE.Mesh).isMesh) {
          const mesh = child as THREE.Mesh;
          mesh.geometry?.dispose();
          if (Array.isArray(mesh.material)) {
            mesh.material.forEach((mat) => mat.dispose());
          } else {
            mesh.material?.dispose();
          }
        }
      });
    };
  }, [scene]);

  return <primitive ref={groupRef} object={scene} scale={1.5} position={[0, -1, 0]} />;
}

// Preload the asset outside the component
useGLTF.preload("/models/product.glb");
```

---

## 5. Custom GLSL Shader Material Pattern

Custom shaders elevate generic 3D into high-end creative web art.

```tsx
// components/canvas/CustomShaderMesh.tsx
"use client";

import { useRef, useMemo } from "react";
import { useFrame } from "@react-three/fiber";
import * as THREE from "three";

const vertexShader = `
  uniform float uTime;
  varying vec2 vUv;
  varying vec3 vNormal;

  void main() {
    vUv = uv;
    vNormal = normal;
    
    // Wave displacement
    vec3 pos = position;
    float wave = sin(pos.x * 3.0 + uTime * 2.0) * 0.15;
    pos.z += wave;

    gl_Position = projectionMatrix * modelViewMatrix * vec4(pos, 1.0);
  }
`;

const fragmentShader = `
  uniform float uTime;
  uniform vec3 uColorA;
  uniform vec3 uColorB;
  varying vec2 vUv;
  varying vec3 vNormal;

  void main() {
    vec3 color = mix(uColorA, uColorB, vUv.y + sin(uTime) * 0.2);
    // Subtle rim lighting
    float rim = 1.0 - max(dot(vNormal, vec3(0.0, 0.0, 1.0)), 0.0);
    color += pow(rim, 3.0) * 0.5;

    gl_FragColor = vec4(color, 1.0);
  }
`;

export function CustomShaderMesh() {
  const materialRef = useRef<THREE.ShaderMaterial>(null!);

  const uniforms = useMemo(
    () => ({
      uTime: { value: 0 },
      uColorA: { value: new THREE.Color("#6366f1") },
      uColorB: { value: new THREE.Color("#ec4899") },
    }),
    []
  );

  useFrame((_, delta) => {
    if (materialRef.current) {
      materialRef.current.uniforms.uTime.value += delta;
    }
  });

  return (
    <mesh>
      <planeGeometry args={[3, 3, 64, 64]} />
      <shaderMaterial
        ref={materialRef}
        vertexShader={vertexShader}
        fragmentShader={fragmentShader}
        uniforms={uniforms}
        wireframe={false}
      />
    </mesh>
  );
}
```

---

## 6. Critical Performance & Memory Rules

1. **Clamp DPR:** Always set `dpr={[1, 2]}` on `<Canvas />`. Never allow `window.devicePixelRatio` above 2 (Retina screens will crush the GPU on 3x).
2. **Never allocate inside `useFrame`:**
   - ❌ `useFrame(() => { const v = new THREE.Vector3(); })` (triggers brutal Garbage Collection pauses).
   - ✅ Allocate reusable vectors/matrices once outside or in a ref.
3. **Always dispose on unmount:** Meshes, geometries, textures, and materials do NOT automatically garbage collect in WebGL. Traverse and call `.dispose()` when scenes unload.
4. **InstancedMesh for duplicates:** If rendering more than 50 of the same object (particles, grass, cubes), use `<instancedMesh>` instead of individual `<mesh>` elements to keep draw calls at 1.

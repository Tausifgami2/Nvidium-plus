# Nvidium++
### GPU-Accelerated Rendering Engine for Minecraft Java Edition

> **Status:** Active Development — Porting to 1.21.11 | Full release targeting Q3 2026

[![Minecraft](https://img.shields.io/badge/Minecraft-1.21.11-brightgreen)](https://minecraft.net)
[![Fabric](https://img.shields.io/badge/Fabric-Latest-blue)](https://fabricmc.net)
[![License](https://img.shields.io/badge/License-LGPL--3.0-orange)](LICENSE.txt)
[![NVIDIA](https://img.shields.io/badge/Requires-NVIDIA%20GTX%201660%2B-76b900)](https://nvidia.com)

---

## What is Nvidium++?

Nvidium++ is a next-generation GPU rendering engine for Minecraft Java Edition, built on top of the original [Nvidium](https://modrinth.com/mod/nvidium) by MCRcortex. It extends the original's revolutionary mesh shader-based terrain rendering with a completely new **GPU-instanced entity rendering system**, making high entity count scenarios — mob farms, item farms, lobbies, particle-heavy areas — dramatically faster.

This is not just a port. It is a fundamental reimagining of what Minecraft's rendering pipeline can look like when the GPU is actually in charge.

---

## Why Does This Exist?

The original Nvidium achieved 6+ million downloads by proving that NVIDIA's mesh shader pipeline could render Minecraft terrain orders of magnitude faster than vanilla. It then went unmaintained, leaving millions of players stuck on 1.21.1 with no updates.

Meanwhile, Minecraft's entity rendering has remained fundamentally broken for high-count scenarios:

- 500 armor stands → slideshow
- 1000 dropped items → unplayable
- 100 players in a lobby → 20 FPS
- Dense mob farms → CPU bottleneck regardless of GPU

Every entity is a separate CPU draw call. Your GPU sits mostly idle while your CPU chokes. Nvidium++ fixes this.

---

## Features

### ✅ Available Now (1.21.1 base)
- NVIDIA mesh shader terrain rendering
- Async occlusion culling
- Sparse addressable VRAM terrain buffer
- Sodium integration

### 🔄 In Progress
- **1.21.11 port** with Sodium 0.8.x API compatibility

### 🗓️ Planned — Phase 2
- **GPU instanced entity rendering** — all mobs, animals, armor stands rendered in minimal draw calls
- **GPU instanced item rendering** — thousands of dropped items at near-zero cost
- **Particle GPU instancing** — 10x particle counts with better performance than vanilla defaults

### 🗓️ Planned — Phase 3
- **Nametag batching** — 100 player nametags in one draw call instead of 100
- **Foliage GPU animation** — wind effects handled entirely on GPU
- **GPU-accelerated lighting** — parallel light propagation via compute shaders

### 💭 Long-term Exploration (No Timeline)
These are ideas being explored after core rendering work is complete. No guarantees, no timeline — but they're on the mind:
- **Shader pipeline support** — G-buffer deferred rendering that would allow custom shader packs to work alongside the mesh shader pipeline. Extremely complex, major undertaking on its own.
- **DLSS 4 integration** — Neural upscaling via NVIDIA Streamline SDK for Blackwell architecture GPUs. Depends on shader pipeline work being completed first.

---

## Performance

> Formal benchmarks will be published after real-world testing across different hardware configurations. Expect significant FPS improvements in high entity count scenarios — mob farms, item farms, lobbies, and particle-heavy areas are the primary targets. Normal gameplay impact will be minimal.

| Scenario | Expected Impact |
|---|---|
| Large mob/animal farms | Significant improvement |
| Thousands of dropped items | Significant improvement |
| High player count lobbies | Significant improvement |
| Heavy particle areas | Significant improvement |
| Normal everyday gameplay | Minimal change |

---

## Requirements

| Requirement | Minimum |
|---|---|
| Minecraft | 1.21.11 (in progress), 1.21.1 (current) |
| Mod Loader | Fabric |
| Sodium | 0.8.x (1.21.11) / 0.6.x (1.21.1) |
| GPU | NVIDIA GTX 1660 or newer (Turing+ architecture) |
| VRAM | 4GB minimum, 8GB+ recommended |
| OS | Windows 10/11, Linux (higher VRAM usage on Linux) |

> **AMD and Intel GPUs are not supported.** This mod uses NVIDIA-exclusive OpenGL extensions (`GL_NV_mesh_shader`, `GL_NV_bindless_texture`, etc.) that have no cross-vendor equivalent. Nvidium++ will automatically detect incompatible hardware and gracefully disable itself — it will not crash.

---

## Compatibility

| Mod | Status |
|---|---|
| Sodium | ✅ Required |
| Lithium | ✅ Compatible |
| C2ME | ✅ Compatible (recommended) |
| Iris Shaders | ⚠️ Disables terrain mesh shader path (shader pipeline support planned Phase 4) |
| Voxy | ✅ Compatible — complementary LOD system |
| FerriteCore | ✅ Compatible |
| ImmediatelyFast | ⚠️ Partial overlap — entity rendering handled by Nvidium++ when active |
| Bobby | ✅ Compatible |

---

## How It Works

### Terrain Rendering (Original Nvidium)
Instead of the traditional vertex → fragment pipeline, Nvidium uses NVIDIA's proprietary mesh shader extensions to generate and cull geometry entirely on the GPU. Terrain chunks are stored in a sparse addressable VRAM buffer and rendered with a single `glMultiDrawMeshTasksIndirectNV` call regardless of chunk count. The GPU handles occlusion, LOD, and geometry emission without CPU involvement per-frame.

### Entity Rendering (Nvidium++ Addition)
Each entity type (cow, zombie, armor stand, dropped item, etc.) maintains a persistent GPU-mapped instance buffer containing per-entity position, rotation, animation state, equipment, and skin data. The CPU writes updated values to this buffer each game tick via persistent mapped memory — no explicit PCIe transfers. The GPU renders all instances of each entity type in a single draw call.

Complex entities (ender dragon, wither boss) and edge cases (chest opening animations, modded entities) fall back to vanilla rendering automatically with no visual artifacts.

---

## Recommended Mod Stack

For maximum performance alongside Nvidium++:

```
Sodium          — base renderer
Lithium         — game logic optimization  
C2ME            — parallel chunk loading (critical for elytra)
Noisium         — faster noise generation
FerriteCore     — memory usage reduction
ScalableLux     — parallel lighting engine
Entity Culling  — don't render what you can't see
Moonrise        — chunk scheduling improvements
```

---

## Building from Source

```bash
git clone https://github.com/YOUR_USERNAME/nvidium-plus
cd nvidium-plus
./gradlew build
```

Requires JDK 21. Output jar in `build/libs/`.

---

## Roadmap

```
Q1 2026  ─── 1.21.11 port + Sodium 0.8.x mixin fixes
Q2 2026  ─── Entity + item GPU instancing
Q3 2026  ─── Particles + nametag batching + foliage animation
Beyond   ─── Shader pipeline + DLSS explored when core is stable
```

---

## Credits

- **MCRcortex** — original Nvidium architecture, mesh shader pipeline, sodium integration
- **CaffeineMC team** — Sodium renderer which Nvidium++ builds upon
- Original Nvidium licensed under LGPL-3.0

---

## License

LGPL-3.0 — see [LICENSE.txt](LICENSE.txt)

---

> *Nvidium++ is an independent project and is not affiliated with NVIDIA Corporation, Mojang Studios, or Microsoft.*

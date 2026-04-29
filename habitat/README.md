# Habitat-Sim Assets

This directory is the recommended home for Habitat-Sim scenes used by the
`habitat` backend.

## Layout

```text
drivers_sim/habitat/assets/scenes/
  example/
    scene.glb
    scene.navmesh
    scene_dataset_config.json
    locations.yaml
```

Only the scene file is required to start the backend. Navigation requires a
navmesh; pass it explicitly with `--navmesh` or place a same-stem sidecar file
next to the scene, for example `scene.glb` and `scene.navmesh`.

## Start

```bash
python3 -m robosim.server \
  --backend habitat \
  --scene drivers_sim/habitat/assets/scenes/example/scene.glb \
  --navmesh drivers_sim/habitat/assets/scenes/example/scene.navmesh
```

The v1 backend exposes `rgb` and `agent_odom` through `control_stubs`. It supports
reset, pose query, sensor snapshots/streams, and navmesh-backed `NavigateTo`.
Joint control, end-effectors, LeRobot replay, depth, and semantic sensors are
reserved for later integration phases.

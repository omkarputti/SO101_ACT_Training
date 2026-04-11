# SO-101 Physical AI Hackathon — Simulation & Execution Guide 🚀

A step-by-step guide to running the SO-101 simulation and executing the pick-and-place pipeline.

---

## Table of Contents

- [1. Environment Preparation](#1-environment-preparation)
- [2. Workspace Setup](#2-workspace-setup)
- [3. Before Running](#3-before-running)
- [4. Running the System](#4-running-the-system)
- [5. Expected Behavior](#5-expected-behavior)
- [Common Issues](#-common-issues)
- [Quick Checklist](#-quick-checklist)

---

## 1. Environment Preparation

Ensure you are inside the provided Docker container. If not, start it and enter:

```bash
# Start container (from project directory)
docker compose up -d

# Enter container
docker exec -it lerobot_hackathon_env bash
```

---

## 2. Workspace Setup

> ⚠️ This step ensures your code runs correctly.

### Replace Workspace

- Extract the provided `workspace` folder
- Replace the existing workspace inside the container

Final path must be:

```
/home/hacker/workspace
```

> ⚠️ Make sure:
> - Folder name is exactly `workspace`
> - Old workspace is fully replaced

### Fallback (if replacement fails)

At minimum:

- Create this file:

```
/home/hacker/workspace/pick_and_place.py
```

- Paste your provided script into it

---

## 3. Before Running

Make sure you are in the correct directory:

```bash
cd /home/hacker/workspace
```

Also ensure:

- You are inside the Docker container
- Workspace is properly available

---

## 4. Running the System

You need **3 separate terminals**.

### ▶️ Terminal 1 — Start Simulation

```bash
python3 src/so101_mujoco/scripts/so101_mujoco_bridge.py \
  --model src/so101_mujoco/mujoco/scene.xml
```

✅ Opens MuJoCo viewer  
✅ Robot + environment visible

---

### 🔗 Terminal 2 — Start ROS Bridge

```bash
python3 src/so101_mujoco/scripts/so101_mujoco_bridge.py \
  --model src/so101_mujoco/mujoco/scene.xml
```

If you get any error:

```bash
source /opt/ros/humble/setup.bash
python3 /home/hacker/workspace/ros_bridge.py
```

---

### 🤖 Terminal 3 — Run Pick & Place

```bash
source /opt/lerobot_venv/bin/activate
python3 /home/hacker/workspace/pick_and_place.py
```

---

## 5. Expected Behavior

Once all three terminals are running:

- Simulation starts
- Bridge connects to ROS2
- Robot:
  - Detects object
  - Moves to object
  - Picks object
  - Places object

---

## ⚠️ Common Issues

| Issue | Fix |
|---|---|
| Workspace not replaced properly | Re-extract and replace the `workspace` folder |
| Folder not named `workspace` | Rename it exactly to `workspace` |
| Not inside Docker container | Run `docker exec -it lerobot_hackathon_env bash` |
| `lerobot_venv` not activated | Run `source /opt/lerobot_venv/bin/activate` before Terminal 3 |
| Running commands in wrong directory | Always `cd /home/hacker/workspace` first |

---

## ✅ Quick Checklist

- [ ] Workspace replaced
- [ ] Inside `/home/hacker/workspace`
- [ ] 3 terminals opened
- [ ] Simulation running (Terminal 1)
- [ ] Bridge running (Terminal 2)
- [ ] Pick & Place script running (Terminal 3)

---

## 🏁 Done

If all steps run without errors — your system is fully operational ✅

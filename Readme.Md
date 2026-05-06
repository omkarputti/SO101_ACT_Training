#  SO101 Arm ACT Training Guide Using SOLO CLI

This repository is made for anyone in the world who owns an SO101 robotic arm.

The datasets in this repository were recorded manually by me using the SO101 robot arm.

The goal is simple:

1. Download the dataset
2. Train the robot using SOLO CLI
3. Run inference
4. Make your SO101 robot perform the task automatically

Current tasks included:

* Pick and Place
* Liquid Pouring

This guide is written in a very simple copy-paste style so even beginners can follow it easily.

This guide shows how to:

1. Install SOLO CLI
2. Download your robotics dataset from Hugging Face
3. Train a robot policy model
4. Test the model
5. Understand which model to use

Everything is written in simple copy-paste steps.

---

##  What This Repository Helps You Do

If you have an SO101 robotic arm, you can use these datasets to teach your robot new skills using imitation learning.

The robot learns by watching demonstrations recorded in the dataset.

You do **NOT** need to manually code robot movement.

The AI model learns from the demonstrations and predicts robot actions automatically.

---

##  Available Datasets

| Dataset | Description |
|--------|-------------|
| `https://huggingface.co/datasets/omkarputti/SO101_pick_and_place` | Pick and Place task |
| `https://huggingface.co/datasets/omkarputti/SO101_liquid_pouring` | Liquid Pouring task |

🔗 Hugging Face profile: [https://huggingface.co/omkarputti](https://huggingface.co/omkarputti)

---

> This guide uses only the **ACT policy** because it is reliable and works well for SO101 robot arm imitation-learning tasks like pick-and-place and liquid pouring.

---

##  System Requirements

**Recommended:**

* Ubuntu Linux
* NVIDIA GPU
* CUDA installed
* Python 3.10+

**Example GPUs:**

* RTX 3050
* RTX 3060
* RTX 4060
* RTX 4090

---

##  Step 1 — Install Basic Tools

Open terminal and copy-paste:

```bash
sudo apt update
sudo apt install git git-lfs python3 python3-pip python3-venv -y
```

Enable Git LFS:

```bash
git lfs install
```

---

##  Step 2 — Login to Hugging Face

Install Hugging Face CLI:

```bash
pip install -U huggingface_hub
```

Login:

```bash
huggingface-cli login
```

Paste your Hugging Face token.

Create token here: [https://huggingface.co/settings/tokens](https://huggingface.co/settings/tokens)

Choose: `Write Access`

---

##  Step 3 — Clone SOLO CLI

Go to Desktop:

```bash
cd ~/Desktop
```

Clone SOLO CLI:

```bash
git clone https://github.com/GetSoloTech/solo-cli
```

Open folder:

```bash
cd solo-cli
```

---

##  Step 4 — Create Python Environment

Create environment:

```bash
python3 -m venv solo-env
```

Activate environment:

```bash
source solo-env/bin/activate
```

Your terminal should now show:

```
(solo-env)
```

---

##  Step 5 — Install SOLO CLI

Install SOLO:

```bash
pip install -e .
```

Install training libraries:

```bash
pip install torch torchvision torchaudio
```

Install LeRobot:

```bash
pip install lerobot
```

---

##  Step 6 — Download Dataset

Move to a workspace folder:

```bash
mkdir -p ~/robot_datasets
cd ~/robot_datasets
```

Download **Pick and Place** dataset:

```bash
git clone https://huggingface.co/datasets/omkarputti/SO101_pick_and_place
```

OR download **Liquid Pouring** dataset:

```bash
git clone https://huggingface.co/datasets/omkarputti/SO101_liquid_pouring
```

---

##  Step 7 — Check Dataset Structure

Run:

```bash
cd S0101_pick_and_place
ls
```

You should see folders like:

```
meta/
data/
videos/
```

 That means the dataset is valid.

---

##  Step 8 — Test Dataset Loading

Go back to SOLO CLI folder:

```bash
cd ~/Desktop/solo-cli
```

Open Python:

```bash
python
```

Paste:

```python
from datasets import load_dataset

dataset = load_dataset("omkarputti/S0101_pick_and_place")
print(dataset)
```

If it prints dataset info —  Everything works.

Exit Python:

```python
exit()
```

---

## 🤖 What Happens During Training?

During training:

* The AI watches robot demonstrations
* Learns robot movement patterns
* Learns how to move the arm
* Learns how to complete the task

After training:

* You get a trained robot AI model
* The model can control the SO101 robotic arm

---

##  Step 9 — Start Training ACT Model

### Train on Pick and Place Dataset

```bash
python -m lerobot.scripts.train \
  --dataset.repo_id=omkarputti/S0101_pick_and_place \
  --policy.type=act \
  --output_dir=outputs/act_pick_place \
  --job_name=act_pick_place_training
```

### Train on Liquid Pouring Dataset

```bash
python -m lerobot.scripts.train \
  --dataset.repo_id=omkarputti/S0101_liquid_pouring \
  --policy.type=act \
  --output_dir=outputs/act_liquid_pouring \
  --job_name=act_liquid_pouring_training
```

---

##  Step 10 — Watch Training

During training you will see:

```
loss: 0.123
step: 100
```

**Lower loss = better learning.**

Training may take 30 minutes to several hours depending on your GPU.

---

##  Step 11 — Find Trained Model

After training:

```bash
cd outputs
ls
```

You will see:

```
act_pick_place/
```

Inside it:

```
checkpoints/
```

Your trained robot model is there.

---

##  What Is Inference?

Inference means using the trained AI model to control the real robot.

After training finishes:

* The robot receives camera observations
* The AI predicts robot arm actions
* The robot performs the task automatically

---

##  Step 12 — Run Inference

```bash
python -m lerobot.scripts.eval \
  --dataset.repo_id=omkarputti/S0101_pick_and_place \
  --policy.path=outputs/act_pick_place/checkpoints/last/pretrained_model
```

---

##  Step 13 — Upload Trained Model to Hugging Face

Create model repo: [https://huggingface.co/new-model](https://huggingface.co/new-model)

Then upload:

```bash
cd outputs/act_pick_place
```

Initialize git:

```bash
git init
```

Add remote:

```bash
git remote add origin https://huggingface.co/YOUR_USERNAME/YOUR_MODEL_NAME
```

Push:

```bash
git add .
git commit -m "upload model"
git push -u origin main
```

---

##  Important Tips

### Tip 1 — Small Datasets

Your dataset is relatively small, which means:

* Training is faster
* Easier debugging
* Good for learning

> ⚠️ But the model may overfit with limited data.

### Tip 2 — More Data = Better Robot

To improve performance:

* Record more trajectories
* Add different lighting
* Vary object positions
* Add failures and recoveries

Robots learn from diversity.

---

## 🛠️ Common Problems

### ❌ Problem: CUDA not found

Check GPU:

```bash
nvidia-smi
```

Check PyTorch CUDA:

```bash
python -c "import torch; print(torch.cuda.is_available())"
```

Should print: `True`

---

### ❌ Problem: Hugging Face login failed

```bash
huggingface-cli login
```

Use your access token.

---

### ❌ Problem: Git LFS missing

```bash
sudo apt install git-lfs -y
git lfs install
```

---

### ❌ Problem: Dataset viewer missing

Sometimes Hugging Face needs 5–15+ minutes to index parquet files. This usually fixes itself automatically.

---

## 📚 Useful Commands

| Command | Description |
|--------|-------------|
| `nvidia-smi` | Check GPU |
| `source solo-env/bin/activate` | Activate environment |
| `deactivate` | Deactivate environment |
| `pip list` | Check installed packages |

---

##  Official SOLO CLI Documentation

For setup errors or advanced commands: [https://github.com/GetSoloTech/solo-cli](https://github.com/GetSoloTech/solo-cli)

The repository contains:

* Installation help
* Training commands
* Inference commands
* Simulation setup
* Troubleshooting
* Dataset tools

---

## 🌍 Community Goal

This repository is designed to help SO101 robotic arm users:

* Share datasets
* Train robot policies
* Improve imitation learning
* Build open-source robotics projects
* Make robotics easier for beginners

**You can also contribute:**

* New datasets
* Improved trajectories
* Better camera angles
* New robot tasks
* Better trained models

---

## 🚀 Dataset Links

| Task | Link |
|------|------|
| Pick and Place | [https://huggingface.co/datasets/omkarputti/SO101_pick_and_place](https://huggingface.co/datasets/omkarputti/SO101_pick_and_place) |
| Liquid Pouring | [https://huggingface.co/datasets/omkarputti/SO101_liquid_pouring](https://huggingface.co/datasets/omkarputti/SO101_liquid_pouring) |

---

##  Robot Hardware

```
SO101 Robotic Arm
```

The datasets were collected manually and uploaded to Hugging Face for imitation-learning training.


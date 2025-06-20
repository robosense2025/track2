# 🤖 RoboSense Track 2: Social Navigation

<div align="center">

**Official Baseline Implementation for Social Navigation**

*Based on Falcon -- "From Cognition to Precognition:
A Future-Aware Framework for Social Navigation" (https://github.com/Zeying-Gong/Falcon)*

[![RoboSense Challenge](https://img.shields.io/badge/RoboSense-2025-blue)](https://robosense2025.github.io/)
[![Track 2](https://img.shields.io/badge/Track-Social%20Navigation-green)](https://robosense2025.github.io/track2)
[![IROS 2025](https://img.shields.io/badge/IROS-2025-red)](https://iros2025.org/)
[![License](https://img.shields.io/badge/License-Apache%202.0-yellow)](LICENSE)

**🏆 Prize Pool: $2,000 USD for Track 2 Winners**

</div>

## 📢 Challenge Overview

**Track 2: Social Navigation** challenges participants to develop advanced RGBD-based perception and navigation systems that empower autonomous agents to interact safely, efficiently, and socially in dynamic human environments. Participants will design algorithms that interpret human behaviors and contextual cues to generate navigation strategies that strike a balance between navigation efficiency and social compliance. Submissions must address key challenges such as real-time adaptability, occlusion handling, and ethical decision-making in socially complex settings.

### 🎯 Challenge Objectives

This track evaluates an agent's ability to perform socially compliant navigation in dynamic indoor environments populated with realistic human agents. Participants must design navigation policies based solely on RGBD observations and odometry, without access to global maps or privileged information.

- **Social Norm Compliance**: Maintain safe distances, avoid collisions, and demonstrate socially acceptable behaviors.
- **Realistic Benchmarking**: Navigate in large-scale, photo-realistic indoor scenes with dynamic, collision-aware humans.
- **Egocentric Perception**: Operate from a first-person perspective, simulating how a robot would perceive its surroundings.

## 🏆 Competition Details

- **Venue**: IROS 2025, Hangzhou, China (Oct 19-25, 2025)
- **Registration**: [Google Form](https://forms.gle/robosense2025)
- **Contact**: robosense2025@gmail.com
- **Awards**: 
  - 🥇 1st Place: $1,000 + Certificate
  - 🥈 2nd Place: $600 + Certificate  
  - 🥉 3rd Place: $400 + Certificate
  - 🌟 Innovation Award: Certificate

## 📊 Official Dataset

This track uses the **RoboSense Track 2 Social Navigation Dataset**, which is based on the **Social-HM3D** and **Social-MP3D** benchmark and provides:

- **Goal-driven Trajectories**: Humans navigate with intent, avoiding random or repetitive paths
- **Natural Behaviors**: Movement includes walking, pausing, and realistic avoidance via ORCA
- **Balanced Density**: Human count is scaled to scene size, avoiding over- or under-crowding
- **Diverse Environments**: Includes 844 scenes for Social-HM3D and 72 scenes for Social-MP3D

### Dataset Statistics

| Dataset         | Num. of Scenes | Scene Types                   | Num. of Humans | Natural Motion |
|----------------|----------------|-------------------------------|------------|----------------|
| **Social-HM3D** | 844            | Residence, Office, Shop, etc. | 0–6        | ✔️              |
| **Social-MP3D** | 72             | Residence, Office, Gym, etc.  | 0–6        | ✔️              |


### Baseline Performance (Phase I)

The Falcon baseline achieves the following performance on the Phase I evaluation set using the **Social-HM3D** datasets (∼1,000 test episodes):

<!-- ``` -->
| Dataset       | Success ↑ | SPL ↑  | PSC ↑  | H-Coll ↓ |
|---------------|-----------|--------|--------|-----------|
| Social-HM3D   | 55.15     | 55.15  | 89.56  | 42.96     |
<!-- ``` -->

**Note**: These baseline results are based solely on depth input.
As the competition supports full RGB-D modalities, participants are encouraged to explore richer representations and surpass the baseline performance.

## 🚀 Quick Start

### 1. **Preparing conda env**

Assuming you have [conda](https://docs.conda.io/projects/conda/en/latest/user-guide/install/) installed, let's prepare a conda env:
```
conda_env_name=falcon
conda create -n $conda_env_name python=3.9 cmake=3.14.0
conda activate $conda_env_name
```

### 2. **conda install habitat-sim & habitat-lab**
Following [Habitat-lab](https://github.com/facebookresearch/habitat-lab.git)'s instruction:
```
conda install habitat-sim=0.3.1 withbullet -c conda-forge -c aihabitat
```

Then, assuming you have [this repositories](https://github.com/Zeying-Gong/habitat-lab) cloned (forked from Habitat 3.0), install necessary dependencies of Habitat.
```
git clone --recurse-submodules https://github.com/robosense2025/track2.git # to download the submodule
cd Falcon
pip install -e habitat-lab
pip install -e habitat-baselines
```

### 3. **Downloading the Social-HM3D & Social-MP3D datasets**

- Download Scene Datasets

Following the instructions for **HM3D** and **MatterPort3D** in Habitat-lab's [Datasets.md](https://github.com/facebookresearch/habitat-lab/blob/main/DATASETS.md).

- Download Episode Datasets

Download social navigation (SocialNav) episodes for the test scenes, which can be found here: [Link](https://drive.google.com/drive/folders/1V0a8PYeMZimFcHgoJGMMTkvscLhZeKzD?usp=drive_link).

After downloading, unzip and place the datasets in the default location:
```
unzip -d data/datasets/pointnav
```
- Download Leg animation

```
wget https://github.com/facebookresearch/habitat-lab/files/12502177/spot_walking_trajectory.csv > data/robots/spot-data/spot_walking_trajectory.csv
```

- Download Multi-agent necessary data

```
python -m habitat_sim.utils.datasets_download --uids hab3-episodes habitat_humanoids hab3_bench_assets hab_spot_arm
```

The file structure should look like this:
```
data
├── datasets
│   └── pointnav
│       ├── social-hm3d
│       │   ├── train
│       │   │   ├── content
│       │   │   └── train.json.gz
│       │   └── val
│       │       ├── content
│       │       └── val.json.gz
│       └── social-mp3d
│           ├── train
│           │   ├── content
│           │   └── train.json.gz
│           └── val
│               ├── content
│               └── val.json.gz
└── scene_datasets
└── robots
└── humanoids
└── versoned_data
└── hab3_bench_assets

```

Note that here the definition of SocialNav is different from the original task in [Habitat 3.0](https://arxiv.org/abs/2310.13724).


### 4. Evaluation Falcon Baseline

The pretrained models can be found in [this link](https://drive.google.com/drive/folders/1Bx1L9U345P_9pUfADk3Tnj7uK01EpxZY?usp=sharing). Download it to `pretrained_model/` under the root directory.

You can evaluate it on the Social-HM3D or Social-MP3D datasets using the following template:

```
python -u -m habitat-baselines.habitat_baselines.run \
--config-name=social_nav_v2/falcon_<dataset>.yaml
```

For example, to run it on the Social-HM3D dataset:

```
python -u -m habitat-baselines.habitat_baselines.run \
--config-name=social_nav_v2/falcon_hm3d.yaml
```

### 5. Docker Evaluation Environment

We provide a standardized Docker environment for remote evaluation to ensure consistency and reproducibility across all submissions.

#### 🧱 Evaluation Docker Image

All submissions will be evaluated inside the following Docker image:

```bash
docker pull zeyinggong/robosense_socialnav:v0.5
```

Participants are strongly encouraged to develop and test their pipelines locally using this image to ensure compatibility with the evaluation server.

#### 📦 Submission Packaging

Submissions will be evaluated inside the container under `/app/Falcon/`. You must place all necessary files in the expected structure as described in the [Submission Format](#submission-format). The evaluator will unzip your `submission.zip` or load your `actions.json` file into `/app/Falcon/input/` inside the container.

- For **Code Submissions (`submission.zip`)**, your `run.sh` entry point will be called directly.
- For **Action Submissions (`actions.json`)**, the evaluator will automatically invoke:

```bash
python -u -m habitat_baselines.eval --config-name=falcon_hm3d_replay.yaml
```

#### 🧪 Local Testing (Recommended)

You can test your submission locally before uploading to EvalAI:

```bash
docker run --rm -it \
    --gpus all \
    --runtime=nvidia \
    -v /path/to/your/submission:/app/Falcon/input:ro \
    -v /path/to/your/data:/app/Falcon/data:ro \
    zeyinggong/robosense_socialnav:v0.5 
```

Inside the container, navigate to `/app/Falcon/` and manually execute your `run.sh` or replay evaluator command to verify correctness.

> **Tip:** You may refer to the provided [Baseline ZIP Submission Example](https://drive.google.com/file/d/161XBisTHS5AjlEP5_p6904TQpMo8plW9/view?usp=sharing) and [Baseline Action Submission Example](https://drive.google.com/file/d/1N-G9jCuysbuDvTeuYpOCCC9IO9PdDhRS/view?usp=sharing) for reference.

#### ⏱ Evaluation Time

- Minival Phase: typically 5–10 minutes.
- Phase 1 Full Evaluation: may take 3–5 hours depending on queue length and inference runtime.

If your submission remains pending for over 48 hours, please open an issue on our GitHub repository: [issues](https://github.com/robosense2025/track2/issues). You may also contact us via email at [robosense2025@gmail.com](mailto:robosense2025@gmail.com) if necessary.

## 🎖️ Challenge Participation

### Submission Requirements
1. **Phase I**: Submit results on public test set with reproducible code
2. **Phase II**: Final evaluation on private test set (same size as Phase I)
3. **Code**: Submit reproducible code with your final results
4. **Model**: Include trained model weights
5. **Report**: Technical report describing your approach

## 📏 Evaluation Metrics

Our benchmark focuses on two key aspects: **task completion** and **social compliance**.

| Metric      | Description |
|-------------|-------------|
| **SR** (Success Rate)       | Fraction of episodes where the robot successfully reaches the goal. |
| **SPL** (Success weighted by Path Length) | Penalizes inefficient navigation. Rewards shorter, successful paths. |
| **PSC** (Personal Space Compliance) | Measures how well the robot avoids violating human personal space. A higher PSC indicates better social behavior. The threshold is set to **1.0m**, considering a **0.3m** human radius and **0.25m** robot radius. |
| **H-Coll** (Human Collision Rate) | The proportion of episodes involving any human collision. Collisions imply task failure. |
| **Total Score** | Weighted combination of the core metrics:  **Total = 0.4 × SR + 0.3 × SPL + 0.3 × PSC**. This score reflects overall navigation quality while implicitly penalizing human collisions. |


### Timeline
- **Registration**: [Google Form](https://forms.gle/robosense2025)
- **Phase I Deadline**: Public test set evaluation (~190 cases)
- **Phase II Deadline**: Private test set evaluation (~190 cases)
- **Awards Announcement**: IROS 2025

## 🔗 Resources

- **Challenge Website**: [robosense2025.github.io](https://robosense2025.github.io/)
- **Track Details**: [Track 2 Page](https://robosense2025.github.io/track2)
- **Track Dataset**: [HuggingFace Dataset](https://huggingface.co/datasets/robosense/datasets/tree/main/track2-social-navigation)
- **Baseline Paper**: [arXiv:2409.13244](https://arxiv.org/pdf/2409.13244)
- **Baseline Model**: [Google Drive](https://drive.google.com/drive/folders/1Bx1L9U345P_9pUfADk3Tnj7uK01EpxZY)

## 📧 Contact & Support

- **Email**: robosense2025@gmail.com
- **Challenge Website**: https://robosense2025.github.io/
- **Issues**: Please use GitHub Issues for technical questions

## 📄 Citation

If you use this baseline or the GeoText-1652 dataset, please cite:

```bibtex
@article{gong2024cognition,
  title={From Cognition to Precognition: A Future-Aware Framework for Social Navigation},
  author={Gong, Zeying and Hu, Tianshuai and Qiu, Ronghe and Liang, Junwei},
  journal={arXiv preprint arXiv:2409.13244},
  year={2024}
}
```

## 🙏 Acknowledgements

- **Falcon Team** for the original benchmark and baseline implementation
- **RoboSense Challenge Organizers** for hosting this competition

---

<div align="center">

**🤖 Ready to navigate safely in the crowd? Register now and compete for $2,000!**

[**📝 Register Here**](https://forms.gle/robosense2025) | [**🌐 Challenge Website**](https://robosense2025.github.io/) | [**📧 Contact Us**](mailto:robosense2025@gmail.com)

Made with ❤️ by the RoboSense 2025 Team

</div>

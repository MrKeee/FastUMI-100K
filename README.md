<div align="center">

# FastUMI-100K
## Advancing Data-driven Robotic Manipulation with a Large-scale UMI-style Dataset

## [[paper](https://arxiv.org/abs/2510.08022)]      [[dataset](https://huggingface.co/datasets/IPEC-COMMUNITY/FastUMI_100k_lerobot)]

</div>

<p align="center">
  <img alt="teaser figure" src="./asset/FastUMI100k.png" width="90%">
</p>
## Overview
FastUMI-100K is a large-scale, high-quality UMI-style dataset designed for data-driven robotic manipulation learning. Featuring over **100K+ demonstration trajectories** across **54 diverse tasks** and hundreds of object types, the dataset provides multi-view wrist-mounted fisheye images and high-frequency end-effector states. To facilitate seamless integration with modern robot learning frameworks, all data has been standardized into the **LeRobot v2.1** format.

## Installation
To use the FastUMI-100K dataset, you need to install the official Hugging Face LeRobot library.
1. Create and activate a conda environment
```bash
conda create -n fastumi python=3.10 -y
conda activate fastumi
pip install huggingface_hub
```
2. Install LeRobot
```bash
git clone https://github.com/huggingface/lerobot.git
cd lerobot
git checkout d602e8169cbad9e93a4a3b3ee1dd8b332af7ebf8
pip install -e .
```
*(For advanced usage or compiling from source, please refer to the official [LeRobot repository](https://github.com/huggingface/lerobot).)*

## Dataset Structure
The dataset is recorded at **20 FPS**. All states and actions are provided in the relative end-effector frame. The dataset is naturally divided into two main configurations:

### Single-Arm Configuration
Used for tasks such as `take_items_out_of_drawer` and `wash_clothes` .
- **Observation Images:** Primary view camera `(720, 1280, 3)`
- **Observation State / Action:** 7-dimensional vector `[x, y, z, roll, pitch, yaw, gripper]`
  - `0:3` Cartesian Position (x, y, z)
  - `3:6` Orientation in Euler angles (roll, pitch, yaw)
  - `6` Gripper width (normalized 0-1)

### Dual-Arm Configuration
Used for bimanual tasks such as `Fold_the_Suit`  and `Pack_Skincare_Products` .
- **Observation Images:** Left camera `(720, 1280, 3)` and Right camera `(720, 1280, 3)`
- **Observation State / Action:** 14-dimensional concatenated vector
  - `0:6` Left Arm: `[left_x, left_y, left_z, left_roll, left_pitch, left_yaw, left_gripper]`
  - `7:13` Right Arm: `[right_x, right_y, right_z, right_roll, right_pitch, right_yaw, right_gripper]`

## Loading FastUMI-100K
It is straightforward to load and iterate through the dataset using the LeRobot API. 

To load a single-arm task:
```python
from huggingface_hub import snapshot_download
from lerobot.datasets.lerobot_dataset import LeRobotDataset

local_dir = snapshot_download(
    repo_id="IPEC-COMMUNITY/FastUMI_100k_lerobot",
    repo_type="dataset",
    allow_patterns="single_arm/take_items_out_of_drawer/*",
    local_dir="./my_local_dataset"
)

dataset_single = LeRobotDataset(root="./my_local_dataset/single_arm/take_items_out_of_drawer")

frame = dataset_single[0]
print(f"Single-arm action shape: {frame['action'].shape}")
```

To load a dual-arm task:
```python
from huggingface_hub import snapshot_download
from lerobot.datasets.lerobot_dataset import LeRobotDataset

snapshot_download(
    repo_id="IPEC-COMMUNITY/FastUMI_100k_lerobot",
    repo_type="dataset",
    allow_patterns="dual_arm/Fold_the_Suit/*",
    local_dir="./fastumi_local_data"
)

dataset_dual = LeRobotDataset(root="./fastumi_local_data/dual_arm/Fold_the_Suit")

frame = dataset_dual[0]
print(f"Dual-arm action shape: {frame['action'].shape}") # Expected: (14,)
```


## License
This dataset is released under the **Apache-2.0 License**.

## Citation
If you find our dataset or paper useful in your research, please cite:
```bibtex
@article{liu2025fastumi100k,
  title={{FastUMI-100K}: Advancing Data-driven Robotic Manipulation with a Large-scale {UMI}-style Dataset},
  author={Liu, Kehui and Jia, Zhongjie and Li, Yang and Zhaxizhuoma and Chen, Pengan and Liu, Song and Liu, Xin and Zhang, Pingrui and Song, Haoming and Ye, Xinyi and Cao, Nieqing and Wang, Zhigang and Zeng, Jia and Wang, Dong and Ding, Yan and Zhao, Bin and Li, Xuelong},
  journal={arXiv preprint arXiv:2510.08022},
  year={2025},
  eprint={2510.08022},
  archivePrefix={arXiv},
  primaryClass={cs.RO},
  url={https://arxiv.org/abs/2510.08022}
}
```


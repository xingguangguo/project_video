# CoDeF: Content Deformation Fields for Temporally Consistent Video Processing

<img src='docs/teaser.gif'></img>

[Hao Ouyang](https://ken-ouyang.github.io/)\*, [Qiuyu Wang](https://github.com/qiuyu96/)\*, [Yuxi Xiao](https://henry123-boy.github.io/)\*, [Qingyan Bai](https://scholar.google.com/citations?user=xUMjxi4AAAAJ&hl=en), [Juntao Zhang](https://github.com/JordanZh), [Kecheng Zheng](https://scholar.google.com/citations?user=hMDQifQAAAAJ), [Xiaowei Zhou](https://xzhou.me/),
[Qifeng Chen](https://cqf.io/)&#8224;, [Yujun Shen](https://shenyujun.github.io/)&#8224; (*equal contribution, &#8224;corresponding author)

#### [Project Page](https://qiuyu96.github.io/CoDeF/) | [Paper](https://arxiv.org/abs/2308.07926) | [High-Res Translation Demo](https://ezioby.github.io/CoDeF_Demo/) | [Colab](https://colab.research.google.com/github/camenduru/CoDeF-colab/blob/main/CoDeF_colab.ipynb)

<!-- Abstract: *This work presents the content deformation field **CoDeF** as a new type of video representation, which consists of a canonical content field aggregating the static contents in the entire video and a temporal deformation field recording the transformations from the canonical image (i.e., rendered from the canonical content field) to each individual frame along the time axis. Given a target video, these two fields are jointly optimized to reconstruct it through a carefully tailored rendering pipeline. We also introduce some decent regularizations into the optimization process, urging the canonical content field to inherit semantics (e.g., the object shape) from the video. With such a design, **CoDeF** naturally supports lifting image algorithms to videos, in the sense that one can apply an image algorithm to the canonical image and effortlessly propagate the outcomes to the entire video with the aid of the temporal deformation field. We experimentally show that **CoDeF** is able to lift image-to-image translation to video-to-video translation and lift keypoint detection to keypoint tracking without any training. More importantly, thanks to our lifting strategy that deploys the algorithms on only one image, we achieve superior cross-frame consistency in translated videos compared to existing video-to-video translation approaches, and even manage to track non-rigid objects like water and smog.* -->

## Requirements

The codebase is tested on

* Ubuntu 20.04
* Python 3.10
* [PyTorch](https://pytorch.org/) 2.0.0
* [PyTorch Lightning](https://www.pytorchlightning.ai/index.html) 2.0.2
* 1 NVIDIA GPU (RTX A6000) with CUDA version 11.7. (Other GPUs are also suitable, and 10GB GPU memory is sufficient to run our code.)

To use video visualizer, please install `ffmpeg` via

```shell
sudo apt-get install ffmpeg
```

For additional Python libraries, please install with

```shell
pip install -r requirements.txt
```

Our code also depends on [tiny-cuda-nn](https://github.com/NVlabs/tiny-cuda-nn).
See [this repository](https://github.com/NVlabs/tiny-cuda-nn#pytorch-extension)
for Pytorch extension install instructions.

## Data

### Provided data

We have provided some videos [here](https://drive.google.com/file/d/1cKZF6ILeokCjsSAGBmummcQh0uRGaC_F/view?usp=sharing) for quick test. Please download and unzip the data and put them in the root directory. More videos can be downloaded [here](https://drive.google.com/file/d/10Msz37MpjZQFPXlDWCZqrcQjhxpQSvCI/view?usp=sharing).

### Customize your own data

We segement video sequences using [SAM-Track](https://github.com/z-x-yang/Segment-and-Track-Anything). Once you obtain the mask files, place them in the folder `all_sequences/{YOUR_SEQUENCE_NAME}/{YOUR_SEQUENCE_NAME}_masks`. Next, execute the following command:

```shell
cd data_preprocessing
python preproc_mask.py
```

We extract optical flows of video sequences using [RAFT](https://github.com/princeton-vl/RAFT). To get started, please follow the instructions provided [here](https://github.com/princeton-vl/RAFT#demos) to download their pretrained model. Once downloaded, place the model in the `data_preprocessing/RAFT/models` folder. After that, you can execute the following command:

```shell
cd data_preprocessing/RAFT
./run_raft.sh
```

Remember to update the sequence name and root directory in both `data_preprocessing/preproc_mask.py` and `data_preprocessing/RAFT/run_raft.sh` accordingly.

After obtaining the files, please organize your own data as follows:

```
CoDeF
│
└─── all_sequences
    │
    └─── NAME1
           └─ NAME1
           └─ NAME1_masks_0 (optional)
           └─ NAME1_masks_1 (optional)
           └─ NAME1_flow (optional)
           └─ NAME1_flow_confidence (optional)
    │
    └─── NAME2
           └─ NAME2
           └─ NAME2_masks_0 (optional)
           └─ NAME2_masks_1 (optional)
           └─ NAME2_flow (optional)
           └─ NAME2_flow_confidence (optional)
    │
    └─── ...
```

## Pretrained checkpoints

You can download checkpoints pre-trained on the provided videos via

| Sequence Name | Config |                           Download                           | OpenXLab | 
| :-------- | :----: | :----------------------------------------------------------: | :---------:| 
| beauty_0 | configs/beauty_0/base.yaml |  [Google drive link](https://drive.google.com/file/d/11SWfnfDct8bE16802PyqYJqsU4x6ACn8/view?usp=sharing) |[![Open in OpenXLab](https://cdn-static.openxlab.org.cn/header/openxlab_models.svg)](https://openxlab.org.cn/models/detail/HaoOuyang/CoDeF)|
| beauty_1 | configs/beauty_1/base.yaml |  [Google drive link](https://drive.google.com/file/d/1bSK0ChbPdURWGLdtc9CPLkN4Tfnng51k/view?usp=sharing) | [![Open in OpenXLab](https://cdn-static.openxlab.org.cn/header/openxlab_models.svg)](https://openxlab.org.cn/models/detail/HaoOuyang/CoDeF) |
| white_smoke      | configs/white_smoke/base.yaml |  [Google drive link](https://drive.google.com/file/d/1QOBCDGV2hHwxq4eL1E_45z5zhZ-wTJR7/view?usp=sharing) | [![Open in OpenXLab](https://cdn-static.openxlab.org.cn/header/openxlab_models.svg)](https://openxlab.org.cn/models/detail/HaoOuyang/CoDeF) |
| lemon_hit      | configs/lemon_hit/base.yaml |  [Google drive link](https://drive.google.com/file/d/140ctcLbv7JTIiy53MuCYtI4_zpIvRXzq/view?usp=sharing) | [![Open in OpenXLab](https://cdn-static.openxlab.org.cn/header/openxlab_models.svg)](https://openxlab.org.cn/models/detail/HaoOuyang/CoDeF)|
| scene_0      | configs/scene_0/base.yaml |  [Google drive link](https://drive.google.com/file/d/1abOdREarfw1DGscahOJd2gZf1Xn_zN-F/view?usp=sharing) |[![Open in OpenXLab](https://cdn-static.openxlab.org.cn/header/openxlab_models.svg)](https://openxlab.org.cn/models/detail/HaoOuyang/CoDeF)|

And organize files as follows

```
CoDeF
│
└─── ckpts/all_sequences
    │
    └─── NAME1
        │
        └─── EXP_NAME (base)
            │
            └─── NAME1.ckpt
    │
    └─── NAME2
        │
        └─── EXP_NAME (base)
            │
            └─── NAME2.ckpt
    |
    └─── ...
```

## Train a new model

```shell
./scripts/train_multi.sh
```

where
* `GPU`: Decide which GPU to train on;
* `NAME`: Name of the video sequence;
* `EXP_NAME`: Name of the experiment;
* `ROOT_DIRECTORY`: Directory of the input video sequence;
* `MODEL_SAVE_PATH`: Path to save the checkpoints;
* `LOG_SAVE_PATH`: Path to save the logs;
* `MASK_DIRECTORY`: Directory of the preprocessed masks (optional);
* `FLOW_DIRECTORY`: Directory of the preprocessed optical flows (optional);

Please check configuration files in ``configs/``, and you can always add your own model config.

## Test reconstruction <a id="anchor"></a>

```shell
./scripts/test_multi.sh
```
After running the script, the reconstructed videos can be found in `results/all_sequences/{NAME}/{EXP_NAME}`, along with the canonical image.

## Test video translation

After obtaining the canonical image through [this step](#anchor), use your preferred text prompts to transfer it using [ControlNet](https://github.com/lllyasviel/ControlNet).
Once you have the transferred canonical image, place it in `all_sequences/${NAME}/${EXP_NAME}_control` (i.e. `CANONICAL_DIR` in `scripts/test_canonical.sh`).

Then run

```shell
./scripts/test_canonical.sh
```

The transferred results can be seen in `results/all_sequences/{NAME}/{EXP_NAME}_transformed`.

*Note*: The `canonical_wh` option in the configuration file should be set with caution, usually a little larger than `img_wh`, as it determines the field of view of the canonical image.

### BibTeX

```bibtex
@article{ouyang2023codef,
      title={CoDeF: Content Deformation Fields for Temporally Consistent Video Processing},
      author={Hao Ouyang and Qiuyu Wang and Yuxi Xiao and Qingyan Bai and Juntao Zhang and Kecheng Zheng and Xiaowei Zhou and Qifeng Chen and Yujun Shen},
      journal={arXiv preprint arXiv:2308.07926},
      year={2023}
}
```

### Acknowledgements
We thank [camenduru](https://github.com/camenduru) for providing the [colab demo](https://github.com/camenduru/CoDeF-colab).

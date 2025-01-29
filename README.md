# PromptStealer Reproduction Project

## Members

- Doyoon Lee, Mobile Computing Lab, Osaka University (33E23033)
- Rami Naeem, Mobile Computing Lab, Osaka University (33E24035)

## Original Paper and Repository

- [USENIX Security 2024 Conference](https://www.usenix.org/conference/usenixsecurity24/presentation/shen-xinyue)
- [Paper](https://www.usenix.org/system/files/usenixsecurity24-shen-xinyue.pdf)
- [Github](https://github.com/verazuo/prompt-stealing-attack)

## Modified & Extended Repositories (Our Version)

- [paper-reproduction-prompt-stealing-attack](https://github.com/dynle/paper-reproduction-prompt-stealing-attack)
- [paper-reproduction-stable-diffusion](https://github.com/dynle/paper-reproduction-stable-diffusion)
## The Target Result

Below is the table from [Prompt Stealing Attacks Against Text-to-Image Generation Models](https://www.usenix.org/system/files/usenixsecurity24-shen-xinyue.pdf) that we aim to reproduce. We reproduce the last row in the table which quantitatively shows the performance of promptStealer.

![Table from the Paper](https://github.com/user-attachments/assets/0c32246c-1086-4722-bc75-34d83f7d3d9d)

*Figure: Table extracted from the paper.*

## Dataset

LexicaDataset is a vast collection of text-to-image prompt pairs, with **61,467 prompt-image pairs** sourced from [Lexica](https://lexica.art/). All prompts are provided by real users, and the images are generated using Stable Diffusion.

You can access the LexicaDataset on [Hugging Face Datasets](https://huggingface.co/datasets/vera365/lexica_dataset).

## Setup the Environment

The following code are run and tested on Ubuntu 24.04.3, Intel Core i9-10980XE, Nvidia RTX A6000 GPU, and 48GB RAM.

The environment requirement:
> cuda toolkit 11.7, python 3.8, pytorch 1.12.0a0+8a1a93a

You can access it from the official docker image provided by Nvidia: [`nvcr.io/nvidia/pytorch:22.05-py3`](https://docs.nvidia.com/deeplearning/frameworks/pytorch-release-notes/rel_22-05.html).

## Steps to Reproduce

1. **Run the docker container with the official docker image mentioned above.**

2. **Clone the repository from [prompt-stealing-attack](https://github.com/verazuo/prompt-stealing-attack) for model and [stable-diffusion](https://github.com/CompVis/stable-diffusion) for making a dataset.**

```
git clone https://github.com/verazuo/prompt-stealing-attack.git
git clone https://github.com/CompVis/stable-diffusion.git
```

- Make sure the folder structure as following.

```
reproduction/
├── prompt-stealing-attack/
└── stable-diffusion/
```

3. **Move to `prompt-stealing-attack` folder and install necessary packages.**

```bash
cd prompt-stealing-attack
pip install -r requirements.txt
```

4. **Run promptStealer to get Semantic Similarity and Modifier Similarity.**

```bash
python3 eval_PromptStealer.py
```

- Usage of PromptStealer
    A script `eval_PromptStealer.py` in prompt-stealing-attaack folder is provided for easy use. To run PromptStealer, first create dir `output/PS_ckpt`, then download two modules/checkpoints of PromptStealer in `output/PS_ckpt/`.

    | Module | Checkpoint |
    | --- | --- |
    | subject generator | [Download](https://drive.google.com/file/d/1OO8fJrsoIR1qH2Ni2oint4bYciG5y8Ma/view?usp=drive_link) |
    | modifier detector | [Download](https://drive.google.com/file/d/1JmhAPzBImiJVw4pnTLa2daBOhNDM_oGc/view?usp=drive_link) |

5. **Move to `stable-diffusion` folder and install necessary packages.**

    ⚠️ **Caution:** Be careful to match version of libraries since we move to another project.
```bash
cd stable-diffusion
pip install -r requirements.txt
```

6. **Download stable diffusion checkpoint from [stable-diffusion-v-1-4-original](https://huggingface.co/CompVis/stable-diffusion-v-1-4-original) and link the weight.**
```bash
mkdir -p models/ldm/stable-diffusion-v1/
ln -s <path/to/model.ckpt> models/ldm/stable-diffusion-v1/model.ckpt
```

7. **Run `generate.py` to make a dataset which has all the images generated by stolen prompts. We provide `run_generate.sh` if you want to run the code in background using nohup.**

```bash
python3 generate.py

# Use below code to run and save log in background
nohup ./run_generate.sh > output.log 2>&1 &
```

8. **Run `EX_calcualte_image_pixel_similarity.py` to get Image Similarity and Pixel Similarity.**
    
    The code makes use of get_image_similarity() and get_pixel_mse() from `utils.py` after genrating all the images from stable diffusion and comparing with the lexica dataset.

```bash
python3 EX_calculate_image_pixel_similarity.py
```

## Things Considered Necessary for Reproduction

### 1. Understanding the Paper
- Identify the core problem, methodology, and key experiment to reproduce.
- Check if the paper provides code, datasets, or supplementary materials.
- Clarify any ambiguities in the evaluation criteria and methodology.

### 2. Rebuilding the Experiment Environment
- Set up the required OS, libraries, and dependencies to ensure compatibility.
- Use virtual environments or containers (Docker) to maintain consistency with the original environment.
- Resolve issues with deprecated or missing dependencies.

### 3. Code Acquisition and Execution
- Verify if the authors provide the source code and check for completeness.
- Address broken dependencies or missing configurations.
- Understand undocumented or outdated code sections.

### 4. Data Availability and Preprocessing
- Ensure access to the required dataset, provide an instruciton how to use it in code, and follow the correct preprocessing steps.
- Handle discrepancies in dataset versions or formats.

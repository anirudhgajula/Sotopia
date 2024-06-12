<div style="width: 100%;">
  <img src="figs/title.png" style="width: 100%;" alt="sotopia"></img>
</div>

# Sotopia (with Reasoning): an Open-ended Social Learning Environment
[![Original Project Page](https://img.shields.io/badge/Project-Page-green.svg)](https://www.sotopia.world/projects/sotopia)
[![Original Paper PDF](https://img.shields.io/badge/Paper-PDF-red.svg)](https://arxiv.org/abs/2310.11667)
[![Python 3.11](https://img.shields.io/badge/python-3.11-blue.svg)](https://www.python.org/downloads/release/python-3109/)
[![pre-commit](https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit&logoColor=white)](https://pre-commit.com/)
<a href="https://github.com/psf/black"><img alt="Code style: black" src="https://img.shields.io/badge/code%20style-black-000000.svg"></a>
[![Checked with mypy](https://www.mypy-lang.org/static/mypy_badge.svg)](https://mypy-lang.org/)
[![bear-ified](https://raw.githubusercontent.com/beartype/beartype-assets/main/badge/bear-ified.svg)](https://beartype.readthedocs.io)
[![Github Action](https://github.com/XuhuiZhou/sotopia/actions/workflows/tests.yml/badge.svg?branch=main)]()
[![Github Action](https://github.com/XuhuiZhou/sotopia/actions/workflows/pre-commit.yml/badge.svg?branch=main)]()
[![Original Dataset](https://img.shields.io/badge/%F0%9F%A4%97-Sotopia%20Dataset-yellow)](https://huggingface.co/datasets/cmu-lti/sotopia)
[![Original Demo](https://img.shields.io/badge/%F0%9F%A4%97-Sotopia%20Demo-orange)](https://huggingface.co/spaces/cmu-lti/sotopia-space/)


## Introduction

As a final project for Caltech CS 159, we modified the Sotopia environment to implement support for testing 3 reasoning strategies: Belief-Desire-Intent (BDI), which guides an agent in thinking through their beliefs, desires, and intent before acting, Emphathetic Reasoning (EMP), which guides agents to predict the beliefs and desires of the other agent before acting, and Multiple Response Optimization (MRO), which guides a agent through generating possible responses and then selecting one. We do this to then use the SOTOPIA environment framework with added reasoning strategies to evaluate how such reasoning strategies improve social intelligence as measured by the SOTOPIA-Eval benchmark, which rates conversations across 7 metrics. We also have updated the SOTOPIA-Eval benchmark to use GPT-4o instead of GPT-4. It was found that all reasoning strategies improve believability (human-like nature) of the conversations, at the cost of increasing persistence and hence decreasing relationship scores. However, EMP reasoning only slightly decreases relationship while largely increasing believability and knowledge and therefore has the highest improvement to the overall score.

## Usage

### Reasoning Demo
After loading environment and character data to the REDIS server, you can run a simple demo with reasoning using the below. Note that reasoning strategy can be passed into the `reasoning` parameter, which is a dictionary holding reasoning strategy for each agent. The reasoning options are `"BDI"`, `"EMP"`, `"MRO"`, `"BDIM"`, `"EMPM"`, `"BDI+EMP"`, and `"BDI+EMPM"`.

```python
from sotopia.samplers.base_sampler import BaseSampler
from sotopia.samplers import UniformSampler

from sotopia.server import LLM_Name, run_async_server

await run_async_server(
        model_dict={
            "env": "gpt-4o",
            "agent1": "gpt-3.5-turbo",
            "agent2": "gpt-3.5-turbo",
        },
        sampler=UniformSampler(),
        reasoning={
            "agent1": "EMP",
            "agent2": ""
        }
    )
```

### Running Experiments
We also add support for running experiments in batch using various reasoning strategies, by adding the `reasoning_eval.py` script under `Sotopia/examples`. For example, to run 30 episode simulations with Agent 1 using BDI reasoning (and Agent 2 using no reasoning by default), run the following:

```bash
mkdir logs
python Sotopia/examples/reasoning_eval.py --gin_file Sotopia/sotopia_conf/generation_utils_conf/generate.gin --gin_file Sotopia/sotopia_conf/server_conf/server.gin --gin_file Sotopia/sotopia_conf/run_async_server_in_batch.gin '--gin.AGENT1_REASONING="BDI"' '--gin.BATCH_SIZE=20' '--gin.PUSH_TO_DB=True' '--gin.TAG="reasoning_BDI_none"' '--gin.TAG_TO_CHECK_EXISTING_EPISODES="reasoning_BDI_none"'
```

## Link to Colab Tests, Analysis, and Data:
<a href="https://drive.google.com/drive/folders/1k5pCjPSx4qf23axOcdXJusr_VbOUtASM?usp=sharing">Google Drive link with experiment code and data</a>
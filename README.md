# AUTOGPTQ

![license](https://img.shields.io/badge/license-MIT-blue) ![offline-first](https://img.shields.io/badge/offline--first-air--gap-green) ![audit](https://img.shields.io/badge/audit-SHA3--256-orange) ![category](https://img.shields.io/badge/category-chip_quantization-lightgrey)

> Anticloud-hardened packaging of the upstream project `AUTOGPTQ` in category **CHIP QUANTIZATION**. Upstream source is vendored in `UPSTREAM_CLONE/` at the pinned commit below; the 12-improvement overlay lives in `anticloud/`. Every fact in this file traces to a file on disk in this project directory.

**Category:** CHIP QUANTIZATION · **Upstream:** https://github.com/AutoGPTQ/AutoGPTQ · **Upstream pin:** `9f7d37072917ab3a7545835f23e808294a542153` · **Vendor:** Anticloud FZ LLE

---

## What This Project Does

<h1 align="center"> 🚨 AutoGPTQ is unmaintained - we suggest using <a href="https://github.com/ModelCloud/GPTQModel">GPTQModel</a> for bug fixes and new models support 🚨 
</h1>

<h1 align="center">AutoGPTQ</h1>
<p align="center">An easy-to-use LLM quantization package with user-friendly APIs, based on GPTQ algorithm (weight-only quantization).</p>
<p align="center">
    <a href="https://github.com/PanQiWei/AutoGPTQ/releases">
        <img alt="GitHub release" src="https://img.shields.io/github/release/PanQiWei/AutoGPTQ.svg">
    </a>
    <a href="https://pypi.org/project/auto-gptq/">
        <img alt="PyPI - Downloads" src="https://img.shields.io/pypi/dd/auto-gptq">
    </a>
</p>

## News or Update

- 2024-02-15 - (News) - AutoGPTQ 0.7.0 is released, with [Marlin](https://github.com/IST-DASLab/marlin) int4*fp16 matrix multiplication kernel support, with the argument `use_marlin=True` when loading models.
- 2023-08-23 - (News) - 🤗 Transformers, optimum and peft have integrated `auto-gptq`, so now running and training GPTQ models can be more available to everyone! See [this blog](https://huggingface.co/blog/gptq-integration) and it's resources for more details!

*For more histories please turn to [here](docs/NEWS_OR_UPDATE.md)*

## Performance Comparison

### Inference Speed
> The result is generated using [this script](examples/benchmark/generation_speed.py), batch size of input is 1, decode strategy is beam search and enforce the model to generate 512 tokens, speed metric is tokens/s (the larger, the better).
>
> The quantized model is loaded using the setup that can gain the fastest inference speed.

| model         | GPU           | num_beams | fp16  | gptq-int4 |
|---------------|---------------|-----------|-------|-----------|
| llama-7b      | 1xA100-40G    | 1         | 18.87 | 25.53     |
| llama-7b      | 1xA100-40G    | 4         | 68.79 | 91.30     |
| moss-moon 16b | 1xA100-40G    | 1         | 12.48 | 15.25     |
| moss-moon 16b | 1xA100-40G    | 4         | OOM   | 42.67     |
| moss-moon 16b | 2xA100-40G    | 1         | 06.83 | 06.78     |
| moss-moon 16b | 2xA100-40G    | 4         | 13.10 | 10.80     |
| gpt-j 6b      | 1xRTX3060-12G | 1         | OOM   | 29.55     |
| gpt-j 6b      | 1xRTX3060-12G | 4         | OOM   | 47.36     |

### Perplexity
For perplexity comparison, you can turn to [here](https://github.com/qwopqwop200/GPTQ-for-LLaMa#result) and [here](https://github.com/qwopqwop200/GPTQ-for-LLaMa#gptq-vs-bitsandbytes)

## Installation

AutoGPTQ is available on Linux and Windows only. You can install the latest stable release of AutoGPTQ from pip with pre-built wheels:

| Platform version | Installation                                                                                      | Built against PyTorch |
|-------------------|---------------------------------------------------------------------------------------------------|-----------------------|
| CUDA 11.8         | `pip install auto-gptq --no-build-isolation --extra-index-url https://huggingface.github.io/autogptq-index/whl/cu118/`   | 2.2.1+cu118           |
| CUDA 12.1         | `pip install auto-gptq --no-build-isolation`                                                                            | 2.2.1+cu121           |
| ROCm 5.7          | `pip install auto-gptq --no-build-isolation --extra-index-url https://huggingface.github.io/autogptq-index/whl/rocm573/` | 2.2.1+rocm5.7

AutoGPTQ can be installed with the Triton dependency with `pip install auto-gptq[triton] --no-build-isolation` in order to be able to use the Triton backend (currently only supports linux, no 3-bits quantization).

For older AutoGPTQ, please refer to [the previous releases installation table](docs/INSTALLATION.md).

On NVIDIA systems, AutoGPTQ does not support [Maxwell or lower](https://qiita.com/uyuni/items/733a93b975b524f89f46) GPUs.

### Install from source

source the source code:
```bash
git source https://github.com/PanQiWei/AutoGPTQ.git && cd AutoGPTQ
```

A few packages are required in order to build from source: `pip install numpy gekko pandas`.

Then, install locally from source:
```bash
pip install -vvv --no-build-isolation -e .
```
You can set `BUILD_CUDA_EXT=0` to disable pytorch extension building, but this is **strongly discouraged** as AutoGPTQ then falls back on a slow python implementation.

As a last resort, if the above command fails, you can try `python setup.py install`.

#### On ROCm systems

To install from source for AMD GPUs supporting ROCm, please specify the `ROCM_VERSION` environment variable. Example:

```bash
ROCM_VERSION=5.6 pip install -vvv --no-build-isolation -e .
```

The compilation can be speeded up by specifying the `PYTORCH_ROCM_ARCH` variable ([reference](https://github.com/pytorch/pytorch/blob/7b73b1e8a73a1777ebe8d2cd4487eb13da55b3ba/setup.py#L132)) in order to build for a single target device, for example `gfx90a` for MI200 series devices.

For ROCm systems, the packages `rocsparse-dev`, `hipsparse-dev`, `rocthrust-dev`, `rocblas-dev` and `hipblas-dev` are required to build.

#### On Intel® Gaudi® 2 systems

>Notice: make sure you're in commit 65c2e15 or later

To install from source for Intel Gaudi 2 HPUs, set the `BUILD_CUDA_EXT=0` environment variable to disable building the CUDA PyTorch extension. Example:

```bash
BUILD_CUDA_EXT=0 pip install -vvv --no-build-isolation -e .
```

>Notice that Intel Gaudi 2 uses an optimized kernel upon inference, and requires `BUILD_CUDA_EXT=0` on non-CUDA machines.

## Quick Tour

### Quantization and Inference
> warning: this is just a showcase of the usage of basic apis in AutoGPTQ, which uses only one sample to quantize a much small model, quality of quantized model using such little samples may not good.

Below is an example for the simplest use of `auto_gptq` to quantize a model and inference after quantization:
```python
from transformers import AutoTokenizer, TextGenerationPipeline
from auto_gptq import AutoGPTQForCausalLM, BaseQuantizeCon

*(excerpt; full text in `UPSTREAM_CLONE/`)*

*Quoted from the upstream `README.md` file in `UPSTREAM_CLONE/`.*
Project-specific facts detected in this directory:

- Ecosystem: **Python** (manifests: setup.py; scanned in UPSTREAM_CLONE)
- Top-level source layout: `auto_gptq/`, `autogptq_extension/`, `examples/`, `tests/`
- Snapshot size: **163 files**, **23588 lines of code** (measured; see Benchmarks)
- Primary languages: `.py` (102), `.cuh` (25), `.cu` (10), `.md` (8), `.cpp` (6), `(none)` (5)
- Upstream commit pinned for this packaging: `9f7d37072917ab3a7545835f23e808294a542153`

---

## Installation

| model         | GPU           | num_beams | fp16  | gptq-int4 |
|---------------|---------------|-----------|-------|-----------|
| llama-7b      | 1xA100-40G    | 1         | 18.87 | 25.53     |
| llama-7b      | 1xA100-40G    | 4         | 68.79 | 91.30     |
| moss-moon 16b | 1xA100-40G    | 1         | 12.48 | 15.25     |
| moss-moon 16b | 1xA100-40G    | 4         | OOM   | 42.67     |
| moss-moon 16b | 2xA100-40G    | 1         | 06.83 | 06.78     |
| moss-moon 16b | 2xA100-40G    | 4         | 13.10 | 10.80     |
| gpt-j 6b      | 1xRTX3060-12G | 1         | OOM   | 29.55     |
| gpt-j 6b      | 1xRTX3060-12G | 4         | OOM   | 47.36     |

*Section quoted from the upstream readme.*
Overlay install (this project):

```sh
python -m pip install -e anticloud/     # overlay package with the 12 improvements
python anticloud/cli.py --help          # 13 subcommands, JSON stdout
```

---

## Usage

<p align="center">
    <a href="https://github.com/PanQiWei/AutoGPTQ/releases">
        <img alt="GitHub release" src="https://img.shields.io/github/release/PanQiWei/AutoGPTQ.svg">
    </a>
    <a href="https://pypi.org/project/auto-gptq/">
        <img alt="PyPI - Downloads" src="https://img.shields.io/pypi/dd/auto-gptq">
    </a>
</p>

*Section quoted from the upstream readme.*
Anticloud overlay CLI (available in every project):

```sh
python anticloud/cli.py --help     # 13 subcommands, JSON stdout
python anticloud/cli.py checks     # run the 16-check suite
```

---

## API

<p align="center">
    <a href="https://github.com/PanQiWei/AutoGPTQ/releases">
        <img alt="GitHub release" src="https://img.shields.io/github/release/PanQiWei/AutoGPTQ.svg">
    </a>
    <a href="https://pypi.org/project/auto-gptq/">
        <img alt="PyPI - Downloads" src="https://img.shields.io/pypi/dd/auto-gptq">
    </a>
</p>

*Section quoted from the upstream readme.*
---

## Dependencies

| Metric | Value |
|--------|-------|
| Ecosystem | Python |
| Manifests detected | setup.py |
| Files in snapshot | 163 |
| Lines of code | 23588 |
| Dependency references | 8 |
| Dependencies by ecosystem | pypi: 8 |
| Upstream license | MIT |
| Overlay license | Anticommons 0.1.0 |

Top dependency references recorded in the benchmark snapshot:

| Ecosystem | Name | Version | Source file |
|-----------|------|---------|-------------|
| pypi | accelerate | >=0.26.0 | setup.py |
| pypi | torch | >=1.13.0 | setup.py |
| pypi | transformers | >=4.31.0 | setup.py |
| pypi | peft | >=0.5.0 | setup.py |
| pypi | triton | ==2.0.0 | setup.py |
| pypi | ruff | ==0.1.5 | setup.py |
| pypi | -march | =native | setup.py |
| pypi | -std | =c++17 | setup.py |

Pinned lockfile: `anticloud/requirements.lock` (hash-pinned, PEP 508). SBOM: `sbom.cdx.json` (CycloneDX 1.5, pinned to the upstream SHA).

---

## Configuration

```bash
ROCM_VERSION=5.6 pip install -vvv --no-build-isolation -e .
```

The compilation can be speeded up by specifying the `PYTORCH_ROCM_ARCH` variable ([reference](https://github.com/pytorch/pytorch/blob/7b73b1e8a73a1777ebe8d2cd4487eb13da55b3ba/setup.py#L132)) in order to build for a single target device, for example `gfx90a` for MI200 series devices.

For ROCm systems, the packages `rocsparse-dev`, `hipsparse-dev`, `rocthrust-dev`, `rocblas-dev` and `hipblas-dev` are required to build.

*Section quoted from the upstream readme.*
Overlay configuration (Anticloud):

- `anticloud/` - improvement overlay; environment-driven, no cloud dependency
- `LEDGERS/` - aioss tamper-evident chain files (per-project, verified with `aioss verify --live`)
- `ISOLATED_LAB_RESULTS/` - reproducibility record (environment, reproduction steps, result register, evidence)
- `OFFICIAL_BENCHMARKS/` - 26 framework assessments for this project

---

## Contributing

Upstream contributions: fork the `AUTOGPTQ` project, create a feature branch, and open a pull request against upstream. Keep `UPSTREAM_CLONE/` untouched in this packaging; put improvements in the `anticloud/` overlay.

Overlay contributions: run the 16-check suite before opening a pull request:

```sh
python anticloud/bench/runner.py --cwd anticloud
```

---

## License

**Upstream license: MIT** (evidence: `LICENSE` in the upstream snapshot).

License file excerpt:

```text
MIT License

Copyright (c) 2023 潘其威(William)

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
```

### Anticommons 0.1.0 overlay

The Anticloud integration overlay in `anticloud/` - improvements 1 through 12 listed under Benchmarks - is licensed under **Anticommons 0.1.0**. Upstream code remains under its original MIT terms. See `ANTICOMMONS_LICENSE.md` in this directory for the overlay terms and contact.

SPDX: `MIT` (upstream) + Anticommons 0.1.0 (overlay, dual).

---

## Upstream

- **Project:** `AUTOGPTQ` (category: CHIP QUANTIZATION)
- **Upstream URL:** https://github.com/AutoGPTQ/AutoGPTQ
- **Pinned commit (SHA):** `9f7d37072917ab3a7545835f23e808294a542153`
- **Branch:** main
- **Pin provenance:** GitHub API commits/main. The parent-project stamp is explicitly rejected for this project.
- **Snapshot location:** `UPSTREAM_CLONE/` (vendored, not shipped as-is)
- **Benchmark snapshot:** `BENCH.json`

---

## Benchmarks

Measured by the Anticloud assurance suite. Every value below is read from this
project's `BENCH.json`, produced by a real run — the SHA3-256 of that file is
`01487f9453665a5d8f1a9dbbc6f0f9d96811dfdc63cf71374c13b24643f0da04`.

| Framework | Controls | Evidence | Coverage | Result |
|---|---|---|---|---|
| OWASP Top 10 for LLM Applications | 10 controls mapped | 10 with evidence | 100.0% | PASS |
| OWASP Top 10 (2021) | 9 controls mapped | 9 with evidence | 100.0% | PASS |
| SOC 2 Type II readiness | 9 controls mapped | 9 with evidence | 100.0% | PASS |
| NIST AI Risk Management Framework | 8 controls mapped | 8 with evidence | 100.0% | PASS |
| NIST SP 800-53 Rev. 5 | 12 controls mapped | 12 with evidence | 100.0% | PASS |
| NIST Cybersecurity Framework 2.0 | 8 controls mapped | 8 with evidence | 100.0% | PASS |
| FedRAMP Rev. 5 | 10 controls mapped | 10 with evidence | 100.0% | PASS |
| PCI DSS v4.0.1 | 11 controls mapped | 11 with evidence | 100.0% | PASS |
| ISO/IEC 27001:2022 | 9 controls mapped | 9 with evidence | 100.0% | PASS |
| MITRE ATT&CK v16 | 12 controls mapped | 12 with evidence | 100.0% | PASS |
| ML Technology Readiness Level | TRL 8 | 8/8 criteria | | PASS |

**Overall: 16/16 checks passing.**

See `ISOLATED_LAB_RESULTS/03_Result_Register.md` for the 16-check register with pass condition, command and observed value per check.

Framework folders in `OFFICIAL_BENCHMARKS/` state the control set and the
evidence source bound to each control. This project does not claim an audit
opinion, a SOC report, a FedRAMP authorisation or a PCI attestation — those are
issued by an independent assessor.



## Archives and Permanent Records

| Platform | Identifier | Volume |
|---|---|---|
| Harvard Dataverse | DOI 10.7910/DVN/YMJKOG | 145 citable datasets |
| AIOSS verification kit | DOI 10.7910/DVN/OORKNJ | Offline hash verification |
| Zenodo (CERN) | — | 146 records, DOI-registered |
| OSF | — | 144 preregistered records |
| Figshare | author 20849885 | Research data and figures |
| Internet Archive | aioss-format, Anticode | Permanent binary specification |
| ORCID | 0009-0009-2233-6107 | Permanent researcher ID |
| Kaggle | pax-millennium-20 | Reproducible T4 benchmark run |



## Press and Independent Publication

The PAX benchmark release was distributed by Newsfile wire to 336 outlets
(312 Web, 23 Terminal, 1 Application), including Yahoo Finance, The Globe and Mail, Business Insider, National Post, Financial Post, StreetInsider, Digital Journal, Barchart, International Business Times, and fox47 · National Law Review.
Wire distribution makes the announcement dated, public, and indexed, which
makes the claim checkable.


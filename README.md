# boosted-wcb

This repository contains the code used in the studies presented in the paper **Novel $|V_{cb}|$ extraction method via boosted bc-tagging with *in-situ* calibration**.

Relevant resources:

- **Paper:** [**2503.00118**](https://arxiv.org/abs/2503.00118)
- **Relevent AI Models:** [**SophonAK4**](https://huggingface.co/jet-universe/sophon-ak4), [**Sophon**](https://huggingface.co/jet-universe/sophon), [**JetClass-II dataset**](https://huggingface.co/datasets/jet-universe/jetclass2)
- **Public datasets:** [**Zenodo**](https://zenodo.org/records/15133378)

The structure and usage of the code are described below, following the typical analysis workflow. 

---

## Sample generation

Samples are generated using a one-stop pipeline with **MadGraph5_aMC@NLO**, **Pythia 8**, and **Delphes 3.5**. Configurations are stored in `gen_configs`, with each process in a separate subfolder, following the convention of the [`jetclass2_generation`](https://github.com/jet-universe/jetclass2_generation) repository.

We use the **JetClass-II Delphes card**, which incorporates track smearing + pileup emulation + pileup mitigation via PUPPI on CMS Run 2/3 conditions. For more details, see the [Delphes card instruction](https://github.com/jet-universe/jetclass2_generation?tab=readme-ov-file#delphes-step).

---

## Delphes analyzers

Delphes outputs are processed using C++ macros in `delphes_analyzers`. These macros convert the Delphes files into analysis-ready *n*-tuples. The usage follows the conventions in `jetclass2_generation` ([link](https://github.com/jet-universe/jetclass2_generation?tab=readme-ov-file#produce-ntuples-from-delphes)). The macros include:

- **`makeNtuplesAK4.C`**: Generates training *n*-tuples for **SophonAK4** using raw JetClass-II Delphes files. It extracts constituent-level features and truth-matching labels for *R* = 0.4 jets.
- **`makeNtuplesEvalSophonAK4.C`**: Evaluates **SophonAK4** on $t\bar{t}$ Delphes samples. Inference is performed on *R* = 0.4 jets to obtain b/c/light tagging discriminants, along with truth labels.
- **`makeNtuplesEvalSophonFatJet.C`**: Evaluates Sophon on boosted jets with $H \to bb/cc/bc$ decays (*R* = 0.8 jets). It performs inference on *R* = 0.8 fatjets and outputs the corresponding bb/cc/bc discriminants and fatjet truth labels.
- **`makeNtuplesWcbAna.C`**: Main macro for boosted-topology analysis. It generates the *n*-tuples for the boosted analysis (`trig1L_1fj` and `trig1L` subfolders found on [**Zenodo**](https://zenodo.org/records/15133378)). This script applies basic boosted-channel selection criteria, processes and stores object-level variables, and performs Sophon and SophonAK4 model inference.
- **`makeNtuplesWcbResolvedAna.C`**: Main macro for resolved-topology analysis. It generates *n*-tuples for resolved analysis (`trig1L_4jets` on [**Zenodo**](https://zenodo.org/records/15133378)).

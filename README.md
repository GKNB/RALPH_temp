# An Active Learning-Based Streaming Pipeline for Reduced Data Training of Structure Finding Models in Neutron Diffractometry

This repository provides the code for the paper titled *"An Active Learning-Based Streaming Pipeline for Reduced Data Training of Structure Finding Models in Neutron Diffractometry"* accepted for publication in **BigData 2024**. This repository includes the codebase necessary to the primary contributions presented in the paper:

1. **Active Learning Algorithm**: A data simulation approach driven by the model's training performance, allowing the same accuracy to be achieved with significantly lesser training data.

2. **Streaming Algorithm**: An enhancement over the traditional serial active learning workflow, utilizing a resource-efficient streaming pipeline to improve overall execution time performance.

The **Active Learning Algorithm** can be executed on any computing platform, while the **Streaming Algorithm** requires a job scheduling system and is currently supported on **Polaris** and **Perlmutter** as reported in the paper.

## Prerequisites

### Environment Setup

#### 1. Install Packages for Training and Active Learning Tasks

- **On Perlmutter:**

  ```bash
  module load pytorch/2.0.1
  ```

- **On Polaris:**

  ```bash
  module use /soft/modulefiles
  module load conda/2024-04-29
  ```

- **On Other Computing Platforms**:

  A list of required packages is provided. A shifter container will be available soon to simplify environment setup. In the meantime, users can use the following command to build the environment:

  ```bash
  conda env create -f environment.yml -n <env_name>
  ```

#### 2. Install GSAS-II for Simulation Tasks:

  ```bash
  g2="https://github.com/AdvancedPhotonSource/GSAS-II-buildtools/releases/download/v1.0.1/gsas2full-Latest-Linux-x86_64.sh"
  curl -L "$g2" > /tmp/g2.sh
  bash /tmp/g2.sh -b -p ~/g2full
  ```

  If you prefer not to install GSAS-II in `~/g2full`, please modify the relevant paths in the executable scripts accordingly.

## Running the Baseline on Polaris

The baseline experiment involves running multiple iterations with varying dataset sizes and random seeds to ensure robustness. This process produces the black error bars shown in Figures 7 and 8. Although the exact results may diff from those in Figure/Table, they will be consistent.

To run the baseline experiment:

  ```bash
  cd workflow
  qsub submit_baseline.sh
  ```

Before executing the command, modify the script as follows:

- Set `seed` (line 12) to: 13010, 13110, 13210, 13310, 13410, 13510
- Set `num_sample` (line 18) to: 40000, 80000, 120000, 160000, 200000, 240000
- Ensure the environment is properly set up (see step 1), and adjust line 9 accordingly. Some example can be found in `env/env_polaris.sh`
- Define `work_dir` as the directory containing this repository (line 13).
- For `num_sample` exceeding 120000, use a different queue (e.g., preemptable) on Polaris, as these tasks cannot complete within an hour.

A total of 36 experiments will be conducted to generate six data points with error bars.

## Running the Serial Workflow on Polaris

In this step, we execute the active learning serial workflow to evaluate:

1. The accuracy of the active learning algorithm.
2. The performance of the serial workflow in terms of runtime.

This experiment produces the black and red error bands in Figures 7 and 8, as well as data for Figures 9 and 10 and Tables III, IV, and V. Although the exact results may diff from those in Figure/Table, they will be consistent.

To run the serial workflow:

  ```bash
  cd workflow
  qsub submit_serial_polaris.sh
  ```

Before executing the command, modify the script as follows:

- Replace `project_name` with your own (line 7).

**For Experiment E1** (data for Figures 7, 8, 9, 10, and Table III):
- Set `seed` (line 12) to: 21000, 21100, 21200, 21300, 21400, 21500
- Set `num_sample` (line 18) to: 4500
- Set `batch_size` (line 23) to: 512

**For Experiment E2** (data for Tables IV and V):
- Set `number_of_nodes` (line 2) to: 1, 2, 4
- Set `queue` (line 6) to: preemptable
- Set `walltime` (line 4) to: 12:00:00
- Set `seed` (line 12) to: 31000
- Set `num_sample` (line 18) to: 72000
- Set `batch_size` (line 23) to: 2048

## Running the Streaming Workflow on Polaris

The final step involves running the active learning streaming workflow to evaluate the runtime performance of this approach. The results are used for Figures 9 and 10 and Tables III, IV, and V. Although the exact results may diff from those in Figure/Table, they will be consistent.

To run the streaming workflow:

  ```bash
  cd workflow
  qsub submit_stream_polaris.sh
  ```

Before executing, ensure the script is configured to match your experimental setup.

- Replace `project_name` with your own (line 7).

**For Experiment E1** (data for Figures 7, 8, 9, 10, and Table III):
- Set `seed` (line 12) to: 22000, 22100, 22200, 22300, 22400, 22500
- Set `num_sample` (line 18) to: 4500
- Set `num_al_sample` (line 22) to: 16200
- Set `batch_size` (line 24) to: 512

**For Experiment E2** (data for Tables IV and V):
- Set `number_of_nodes` (line 2) to: 1, 2, 4
- Set `queue` (line 6) to: preemptable
- Set `walltime` (line 4) to: 12:00:00
- Set `seed` (line 12) to: 32000
- Set `num_sample` (line 18) to: 72000
- Set `num_al_sample` (line 22) to: 259200
- Set `batch_size` (line 24) to: 2048

COMPLETE ARTIFACTS
Download and unzip go2-senior-submission.zip from this repository. It contains
all 52 experiment files, including the three checkpoint/video directories,
resolved configurations, original TensorBoard logs, and reproduction scripts.
The notebook, report and comparison plot are also available directly here.
Archive SHA256: 741feb369c1159346aa240139cd4a049f797ca5925c05ba32549ab39299851a0

Unitree Go2: senior-track locomotion experiments
20 October 2026

Read report.txt for the prediction, measured results, and interpretation.
training_comparison.png shows all three training curves. Numbers in the report
are means of the last 30 logged observations, not the last single value.

Experiments
  baseline:  x/y commands [-1, 1] m/s; 300 default training iterations
  wide:      x/y commands [-2, 2] m/s; 300 training iterations
  wide_long: x/y commands [-2, 2] m/s; 900 training iterations, fresh initialization
All three used seed 42, 4096 environments, and 24 rollout steps per iteration.
Angular commands and all reward/PPO settings were preserved.

Each experiment directory contains:
  checkpoint.pt       Final trained checkpoint (original model_299.pt or model_899.pt)
  policy.mp4          Real policy playback recorded from the Newton GL visualizer
  params/             Resolved environment and PPO configurations
  events.out.tfevents.* Original TensorBoard event data
  playback-command.json Recording command used for this checkpoint

Other evidence
  Go2_Senior_Experiments.ipynb Clean notebook source, pinned to the tested revision
  pretraining-notes.txt       Original notes, preserved as written before training
  *-command.json             Exact training commands
  *-console.log              Training and recording diagnostics
  environment.json           Upstream revision, GPU, driver, and task identifier
  scalars.csv                Original values for the three compared metrics
  metrics_summary.json       Final-window means, standard deviations, and final values
  analyze_runs.py            Scalar export and comparison-plot script
  upstream_config/           Unmodified upstream Go2/task configuration sources
  upstream_uv.lock           Upstream dependency lockfile
  playback_display.py        Display-only adjustment for software OpenGL on Colab
  UPSTREAM_LICENSE           License for the redistributed upstream configuration files

The original notes describe the state before training; their "pending" text is
historical. The completed results are in report.txt and metrics_summary.json.
The notebook's outputs and personal execution metadata have been removed.

Reproduce on a compatible NVIDIA GPU machine

Use the official Newton-only installation. No Isaac Sim installation is needed.
The tested setup was Linux in Colab with a Tesla T4, driver 580.82.07, managed
Python 3.12.15, PyTorch 2.11.0+cu130, and Warp 1.17.0.

  python -m pip install uv
  git clone https://github.com/isaac-sim/IsaacLab.git
  cd IsaacLab
  git checkout 5ffd3d5ac0b4d77d31ea820930a8f5fcac673d87
  uv sync --extra video

Run each command from the IsaacLab checkout, without a visualizer:

  uv run --no-sync isaaclab train --rl_library rsl_rl --task Isaac-Velocity-Flat-UnitreeGo2 --run_name baseline --logger tensorboard

  uv run --no-sync isaaclab train --rl_library rsl_rl --task Isaac-Velocity-Flat-UnitreeGo2 --run_name wide --logger tensorboard --max_iterations 300 'env.commands.base_velocity.ranges.lin_vel_x=[-2.0,2.0]' 'env.commands.base_velocity.ranges.lin_vel_y=[-2.0,2.0]'

  uv run --no-sync isaaclab train --rl_library rsl_rl --task Isaac-Velocity-Flat-UnitreeGo2 --run_name wide_long --logger tensorboard --max_iterations 900 'env.commands.base_velocity.ranges.lin_vel_x=[-2.0,2.0]' 'env.commands.base_velocity.ranges.lin_vel_y=[-2.0,2.0]'

The two wide commands do not resume from a checkpoint. Default seed 42 is used.
These examples use Linux shell quoting; the notebook passes arguments directly.

Replay a saved checkpoint from this submission, using its absolute path:

  uv run --no-sync isaaclab play --rl_library rsl_rl --task Isaac-Velocity-Flat-UnitreeGo2 --checkpoint /absolute/path/to/baseline/checkpoint.pt --num_envs 1 --video newton_gl --video_length 1000

For wide policies, append the same two command-range overrides used in training.
For lighter rendering on Colab, add this submission directory to PYTHONPATH and
append --external_callback playback_display.configure. It selects a 640x360
Newton GL view with shadows disabled; it does not modify the trained policy or
the physics/task/reward settings. The baseline recording used the default view.
Videos are captured directly from the headless Newton visualizer, rather than
from a desktop screen recorder. See video_metadata.json for their actual sizes
and durations.

TensorBoard and analysis

  uv run --no-sync tensorboard --logdir /absolute/path/to/this/submission

Filter tags to error_vel_xy|success_rate|base_contact. Original training runs
remain separately named in the event files. The upstream logs used to generate
the comparison were under logs/rsl_rl/unitree_go2_flat in the IsaacLab checkout.

  uv run --no-sync python /absolute/path/to/analyze_runs.py logs/rsl_rl/unitree_go2_flat /absolute/path/to/analysis-output

Use a new output location for reruns. The analysis script deliberately rejects
ambiguous duplicate run directories and missing/non-finite metrics.

Assignment:
https://newuu-engineering.github.io/internship/tasks/senior/
Official installation guide:
https://isaac-sim.github.io/IsaacLab/main/source/isaaclab/installation.html

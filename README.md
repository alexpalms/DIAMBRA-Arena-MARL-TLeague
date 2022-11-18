# DIAMBRA Arena MARL | TLeague

Multi Agent Reinforcement Learning for DIAMBRA Arena environments using TLeague framework

<img src="https://github.com/alexpalms/DIAMBRA-Arena-MARL-TLeague/blob/main/img/marl.gif?raw=true" alt="marlGif" width="100%"/>

## How to setup the environment

### Prerequisites

Steps of the next section will install all required packages, and in particular DIAMBRA Arena, a new Reinforcement Learning platform for research and
experimentation. For doing so, some prerequisites are required, in particular:

- [Create an account on DIAMBRA website](https://diambra.ai/register/)

- Install Docker Desktop ([Linux](https://docs.docker.com/desktop/install/linux-install/) | [Windows](https://docs.docker.com/desktop/windows/install/) | [MacOS](https://docs.docker.com/desktop/mac/install/)) and make sure you have permissions to run it ([see here](https://docs.docker.com/engine/install/linux-postinstall/)). On Linux, it’s usually enough to run `sudo usermod -aG docker $USER`, log out and log back in.

- Download ROMs, set environment variables and test the installation as described in the installation section of the documentation [here](https://docs.diambra.ai/#installation).

### Python packages installation

- Create a virtual environment (e.g. using Conda or VirtualEnv) and activate it:
  ```
  conda create -n diambra-tleague python=3.7
  conda activate diambra-tleague
  ```
- Clone the repository with its submodules:

  `git clone --recurse-submodules git@github.com:alexpalms/DIAMBRA-Arena-MARL-TLeague.git`

- From inside the repository, install all the submodules using pip:
  - TGym:
    ```shell
    cd TGym
    pip install -e .
    ```
  - TArena:
    ```shell
    cd TArena
    pip install -e .
    ```
  - TPolicies:
    ```shell
    cd TPolicies
    pip install -e .
    ```
  - TLeague:
    ```shell
    cd TLeague
    pip install -e .
    ```

## How to run the MARL training on a single machine (without remote inference)

- Go the TLeague examples folder, from the repo root:

  `cd TLeague/examples/`

- Execute the four TLeague modules:

  - Model pool:

    `bash example_diambra_arena_sp_ppo.sh model_pool`

  - League manager:

    `bash example_diambra_arena_sp_ppo.sh league_mgr`

  - (A single) Learner:

    `diambra run bash example_diambra_arena_sp_ppo.sh learner`

  - (As many) Actor(s as your system supports):

    `diambra run bash example_diambra_arena_sp_ppo.sh actor`

**Note that the Learners and the Actor(s), are launched using DIAMBRA Command Line Interface, that takes care of launching the docker container where the environment image is executed.**

## Relevant customizations

- The following changes have been made to TLeague repository:

  - Created `tleague/envs/diambra_arena/` folder with DIAMBRA Arena interface, featuring:

    - `create_diambra_arena_envs.py` file implementing the methods to instantiate DIAMBRA Arena environment (with the proper TLeague interface) and to retrieve its Action and Observation Spaces
    - `wrapper.py` file containing an environment wrapper (`TLeagueWrapper`) that builds the interface between DIAMBRA Arena and TLeague
    - `create_diambra_arena_envs_test.py` file to test the proper functioning of DIAMBRA Arena TLeague interface
    - `__init__.py` file with generic module imports

  - Adapted `tleague/envs/create_envs.py` methods to accomodate DIAMBRA Arena environments interface
  - Added required dependencies to `setup.py` to fix latest protobuf breaking changes, adding OpenCV and DIAMBRA Arena packages
  - Added script to run all TLeague modules for DIAMBRA Arena at `examples/example_diambra_arena_sp_ppo.sh`, starting from the one used for Pong2D, with the following modifications:
    - Changed the max number of players to: `'max_n_players': 16`
    - Changed the environment identifier to: `env=diambra.arena_doapp`
  - Added run instruction to docs at `docs/EXAMPLE_DIAMBRA_ARENA_SP_PPO.md`

- Original TPolicies repo has gym pinned to an old version `gym==0.12.1`, which doesn't feature the `__getitem__(self, key)` for `Dict` observations. It has been added in the custom TGym which is derived from the 0.12.1 version and the correspondent dependency in TPolicies `setup.py` has been commented out.

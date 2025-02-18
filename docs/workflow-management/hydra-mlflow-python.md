# Configuring with Hydra and logging on MLflow

This guide documents the steps to use Hydra and to log your results on Dagshub's MLflow 
server on python. Note that this section includes many of my personal preferences. Feel 
free to share your ideas in case you find them more efficient or better.
First I will walk you through Hydra on clusters. Then, Dagshub's MLflow server and connecting it with Hydra.

## 1. Hydra
Hydra is an open-source Python framework that simplifies the development of research and 
other complex applications, which is what we often work on, having many different 
experiments with varying configurations in a single project. And yes, hydra simplifies 
that. I won't mention about how everything works on Hydra since there is a beautiful
[guide](https://hydra.cc/docs/intro/) on their website. Instead, I will mention about 
what might really be useful for us such as running multiple experiments in parallel on 
Slurm or HPC cluster with a single line.


I have a template I use as the base of every project I have on
[berab/hydra_template](github.com/berab/hydra_template). I will consider an even 
more simplified project in each section. 

A very simple main source file `src/main.py` looks like:
```python
import hydra
from omegaconf import DictConfig
from dataclasses import dataclass
from hydra.utils import instantiate

@dataclass
class Main:
    proj_name: str
    seed: int
    debug: int

@hydra.main(config_path="../conf/", config_name="main", version_base='1.2')
def main(cfg: DictConfig):
    cfg = instantiate(cfg)
    print(f'Configured with seed {cfg.seed}!')

if __name__ == "__main__":
    main()
```
And its corresponding config file `conf/main.yaml` looks like:
```yaml
_target_: main.Main
proj_name: hydra_template
defaults:
  - _self_

seed: 47
debug: 0
```

### 1.1. Hydra on Slurm

with hydra you can run multiple experiments swept on `seed` and `debug` arguments
with the line:
```sh
python src/main.py --multirun seed=45,12,51 debug=0,1
```
This would run 6 (3x2) experiments sequentially. To run those on Slurm in parallel, we
can add another config file `slurm.yaml`:
```yaml
defaults:
  - override hydra/sweeper: submitit_slurm
hydra:
  launcher:
    partition: long-disi
    timeout_min: 2880
    nodes: 1
    tasks_per_node: 1
    cpus_per_task: 8
    mem_per_cpu: 2048M
    gres: gpu:1
```

Then you should also add this configuration to your main config file `conf/main.yaml`:
```yaml
...
defaults:
  - _self_
  - slurm
...
```

This is the default configuration I often use but you can change it however you want.
After, you can run multiple experiments
```sh
python src/main.py --multirun seed=45,12,51
```
Note that for this to run, we should install another python package you can find in
`requirements.txt` in hydra_template repository.

### 1.2. Hydra on HPC
[hpc_url]: https://docs.google.com/document/d/1u8aIAxgXTUoavdkOkAA5DX-COn0NhYeqdg-uYGgcdGs/edit?tab=t.0
[pbs_launcher_repo]: https://github.com/berab/hydra/tree/main/plugins/pbs_launcher_plugin
[UniTN HPC cluster][hpc_url] uses PBS launcher which Hydra doesn't support. I wrote a 
code merging pbs4py framework and hydra's sweeper. You can find the code repository and
how to install it [here][pbs_launcher_repo].

After the installation, you should create a config file `pbs.yaml`:
```yaml
defaults:
  - override hydra/sweeper: pbs
hydra:
  sweeper:
    job_name: ${proj_name}
    queue_name: common_cpuQ
    ncpus_per_node: 1
    ngpus_per_node: 0
    queue_node_limit: 10
    time: 8
    mem: 16
    profile_file: ~/.bashrc # Sources before starting
```

Again, your main config file `conf/main.yaml` should be:
```yaml
...
defaults:
  - _self_
  - pbs
...
```
Again, you should change the way you need `pbs.yaml`. I activate my python environment
in `~/.bashrc`, but if you don't do that there, you should add this bash file 
to `profile_file`.
I also have a script because of the queue limit (30) of HPC server so that if the scripts 
exceeds the limit, it waits n seconds and check until the limit is sufficient. 
In hpc server, having the script `wait_limit.sh`:
```sh
while [ $(qstat | grep renanberan.kilic | wc -l) -gt 24 ]; do
  echo "waiting for 30m"
  sleep 1800  # Wait before submitting the next batch
done
echo "A job is starting"
python src/main.py --multirun seed=47,23,14 debug=0,1
```

How to run:
```sh
nohup bash wait_limit.sh > wait.out &
```

## 2. Dagshub's MLflow server
I use [Dagshub](https://dagshub.com)'s MLflow for logging the results of my 
experiments. Dagshub have a free MLflow server and you can also connect Github 
repositories to Dagshub if needed to use its server.

### 2.1. Connecting MLflow and Hydra
After having a Dagshub repository we can use its name and your account's token to 
connect and log your results.
But first, you should create an MLflow experiment on your Dagshub's MLflow page. 
For me, it is <https://dagshub.com/berab/hydra_template.mlflow>.
This will create a new experiment with id. We will use this id later on in our source code.
For these infos, i create another config file `dagshub.yaml`:
```yaml
username: berab
token: my_token
```
and adding this to your main config:
Then you should also add this configuration to your main config file `conf/main.yaml`:
```yaml
...
defaults:
  - _self_
  - dagshub
...
```

And in your source code `src/main.py`, you can connect to Dagshub's MLflow server as:
```python
...
import dagshub
import mlflow

@dataclass
class Main:
    ...
    username: str
    token: str

@hydra.main(config_path="../conf/", config_name="main", version_base='1.2')
def main(cfg: DictConfig):
    cfg = instantiate(cfg)

    dagshub.init(cfg.proj_name, cfg.username, mlflow=True)
    mlflow.environment_variables.MLFLOW_TRACKING_PASSWORD = cfg.token
    mlflow.start_run(experiment_id=0)
    mlflow.log_param('seed', cfg.seed)
    print(f'Configured with seed {cfg.seed}!')
```

Note: Dagshub's server speed is quite limited, having multiple parallel 
experiments might result in time our errors. To solve these:
```python
os.environ["_MLFLOW_HTTP_REQUEST_MAX_RETRIES_LIMIT"] = "1000"
os.environ["_MLFLOW_HTTP_REQUEST_MAX_RETRIES"] = "999"
```
Though, this should introduce quite an overhead.

# Alpa: Automating Inter- and Intra-Operator Parallelism for Distributed Deep Learning
This is the artifact for the paper "Alpa: Automating Inter- and Intra-Operator Parallelism for Distributed Deep Learning".
We are going to reproduce the main results in the paper.

## Setting up the Environment
All experiments will be run on an AWS cluster. We provide credentials for you to launch and connect to the cluster.

### Install AWS Credentials (3 mins)
Replace `<access key id>` and `<secrete access key>` in the following commands with the
values provided in the README file on hotcrp. 
Please backup your old credentials before running the following commands if necessary.
```bash
mkdir -p ~/.aws
echo "[default]" > ~/.aws/credentials
echo "aws_access_key_id = <access key id>" >> ~/.aws/credentials
echo "aws_secret_access_key = <secret access key>" >> ~/.aws/credentials
```

### Install Ray (5 mins)
We use ray and boto3 to launch the cluster.
Run the following command on your local machine to install ray. 
```bash
pip3 install -U "ray[default]" boto3
```
Follow [this page](https://docs.ray.io/en/latest/ray-overview/installation.html#m1-mac-apple-silicon-support) if you are using an M1 Mac.

### Launch the Cluster (5 mins)
Clone this repository to your local machine and go to `alpa/osdi22_artifact/`. Start the cluster by
```bash
ray up -y artifact-cluster.yaml
```

After the cluster is started, you can log in to the head node of the cluster by
```bash
ray attach artifact-cluster.yaml
```

You can use the following command to shutdown the cluster. DO NOT RUN IT NOW.
**Make sure to run it after your finish the artfact evaluation or want to pause.**
The price of this AWS cluster is expensive: $100/hour paid by us.
```bash
ray down -y artifact-cluster.yaml
```

### Check the Cluster Status (5 mins)
1. Connect to the cluster by
  ```bash
  ray attach artifact-cluster.yaml
  ```
  and go to the following directory:
  ```bash
  cd efs/alpa/osdi22_artifact/
  ```
2. Wait until all worker nodes are launched. This can take a couple of minutes.
  ```bash
  python3 wait_workers.py
  ```
3. Test installation by
  ```bash
  python3 test_install.py
  ```

  You should be able to see it outputs
  ```
  ...
  Ran 2 tests in 177.621s

  OK
  ```

## End-to-end Performance (Figure. 8)
The profiling and optimization for all data points in our paper take more than 10 hours to run.
To make the artifact evaluation feasible in a reasonable amount of time, we skip some profiling and
optimization procedures, but directly load the solutions found by our system and measure their throughputs.
Optionally, if you want to run the profiling and optimization from scratch by yourself, follow the
instructions in the section "Running the Search" below.

### GPT (30 mins)
```bash
python3 gen_data_e2e.py --model gpt
python3 plot_e2e.py --model gpt
```
This outputs Figure 8 (a).

### MoE (30 mins)
```bash
python3 gen_data_e2e.py --model moe
python3 plot_e2e.py --model moe
```
This outputs Figure 8 (b).

### Wide-ResNet (30 mins)
```bash
python3 gen_data_e2e.py --model wresnet
python3 plot_e2e.py --model wresnet
```
This outputs Figure 8 (c).

## Intra-op Ablation Study (Figure. 9)

### GPT  (30 mins)
```bash
python3 gen_data_intra_ab.py --model gpt
python3 plot_intra_ab.py --model gpt
```
This outputs Figure 9 (a).

### MoE (45 mins)
```bash
python3 gen_data_intra_ab.py --model moe
python3 plot_intra_ab.py --model moe
```
This outputs Figure 9 (b).

### Wide-ResNet (30 mins)
```bash
python3 gen_data_intra_ab.py --model wresnet
python3 plot_intra_ab.py --model wresnet
```
This outputs Figure 9 (c).

## Inter-op Ablation Study (Figure. 10)

```bash
python3 gen_data_inter_ab.py
python3 plot_inter_ab.py
```

## (Optional) Running the Search
To run the full search precedure, use the command below
```
python3 gen_data_e2e.py --model gpt --search
```

## (Optional) Running the Megatron-LM and Deepspeed
The performance numbers of Megatron-LM and Deepspeed in the previous plots are
loaded from our previous measurments in `results_e2e_ref.tsv`.
The data is obtained as follows.

### Megatron-LM Performance on GPT (30 mins)
Following the instructions in [megatron/README.md](megatron/README.md) to run Megatron-LM benchmarking.
This generates data for the red bar corresponding to `Megatron-LM` in Figure 8 (a).

### DeepSpeed Performance on MoE (30 mins)
Following the instructions in [deepspeed/README.md](deepspeed/README.md) to run DeepSpeed benchmarking.
This generates data for the brown bar corresponding to `DeepSpeed` in Figure 8 (b).
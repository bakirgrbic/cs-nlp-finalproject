# electra-elc

## Description
Final project for CS 436 to learn more about pre-training and time 
optimizations to the process.

NOTE:
This project is still a work in progress.
- ELC-BERT doesn't work with eval_wos.py and eval_blimp.sh. Not tested for finetune.sh
- ELECTRA-ELC doesn't work with eval_wos.py and not tested for finetune.sh
- ELECTRA-PT work with all scripts (This is a vanilla ELECTRA-tiny)

## How to Run
Tested on MacOS and AWS Sagemaker environments. 
When running on AWS Sagemaker make sure to install the `transformers` and 
`torch` libraries. To use any pretrained models from this repository do the following:
1. Install [git-lfs](https://git-lfs.com/)
2. Run the following commands to pull the four models
```shell
git lfs fetch --all
```
```shell
git lfs checkout
```

### Data
Make sure to download necessary data as described in [data/README.md](./data/README.md). All data must be in 
the `data` directory except for the BabyLM 2024 evaluation pipeline data which must be placed in the root of
`evaluation/evaluation-pipeline-2024`.

### Pretraining

To pretrain a model from huggingface use the following command:
```shell
./pretrain_hf.py [-h] [--model-config MODEL_CONFIG] [--tokenizer-config TOKENIZER_CONFIG] [--epochs EPOCHS] [--lr LR]
```

To pretrain the ELECTRA_ELC model use the following command:
```shell
./pretrain_electra_elc.py [-h] [--epochs EPOCHS] [--lr LR]                                                                                                                   
```

### Evaluation
#### Web of Science Classification

To pretrain the ELECTRA_ELC model use the following command:
```shell
./eval_wos.py [-h] [--model MODEL] [--tokenizer-config TOKENIZER_CONFIG] [--max-len MAX_LEN] [--batch-size BATCH_SIZE] [--epochs EPOCHS] [--lr LR]
```

#### evaluation-pipeline-2024
1. Pull the submodule repository using the following commands:
```shell
git submodule init
```
```shell
git submodule update
```
2. Follow [Install](https://github.com/babylm/evaluation-pipeline-2024/tree/main?tab=readme-ov-file#install) and [Data](https://github.com/babylm/evaluation-pipeline-2024/tree/main?tab=readme-ov-file#data) instructions from the original evaluation repository. If using AWS install all libraries with pip in a terminal.
3. Make sure `evaluation_data` directory is in the root of `evaluation-pipeline-2024` project
4. Move files from `tokenizer` directory to the `checkpoint/MODEL_NAME` for the model that will be evaluated
5. Open a terminal in the the root of `evaluation-pipeline-2024` project.

To Evaluate on BLiMP first modify the `./eval_blimp.sh` script to be the following
```shell
#!/bin/bash

MODEL_PATH=$1
MODEL_BASENAME=$(basename $MODEL_PATH)

python -m lm_eval --model hf-mlm \
    --model_args pretrained=$MODEL_PATH,backend="mlm" \
    --tasks blimp_filtered,blimp_supplement \
    --device cuda:0 \
    --batch_size 1 \
    --log_samples \
    --output_path results/blimp/${MODEL_BASENAME}/blimp_results.json
```
Run the following command to evaluate on BLiMP for given model path such as ELECTRA_PT.
```shell
./eval_blimp.sh ../../checkpoints/ELECTRA_PT
```

To Evaluate on (Super)GLUE run the following command:
```shell
./finetune_model.sh PATH_TO_MODEL
```
NOTE: If (Super)GLUE evalution is not working make sure transormers lib is version 4.49.0 

Results for these evaluations will be found in the `evaluation-pipeline-2024/results`


## Acknowledgments
Code from [models/electra_elc.py](./models/electra_elc.py) is a derivative work
of [transformers ELECTRA model](https://github.com/huggingface/transformers/blob/v4.51.3/src/transformers/models/electra/modeling_electra.py) 
and [ELC-BERT zero initialization model](https://github.com/ltgoslo/elc-bert/blob/main/models/model_elc_bert_zero.py).

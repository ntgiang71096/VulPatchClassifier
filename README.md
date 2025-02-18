# MiDas - Multi-granularity Detector for Vulnerability Fixing Commits

MiDas is a transformer-based novel techinique for detecting vulnerability-fixing commits. MiDas extract information of commit in respect to multiple levels of granularity (i.e. commit level, file level, hunk level, line level)

MiDas consists of seven feature extractors, regard the combination of granularity and CodeBERT representation:


| Feature extractor index | Granularity | CodeBERT representation |
|------------------|-------------|-------------------------|
| 1                | Commit      | Bimodal                 |
| 2                | File        | Bimodal                 |
| 3                | Hunk        | Bimodal                 |
| 5                | Commit      | Unimodal                |
| 6                | File        | Unimodal                |
| 7                | Hunk        | Unimodal                |
| 8                | Line        | Unimodal                |


To replicate the training process of MiDas, please follow the below steps:

        1. Finetune CodeBERT for each feature extractor
        2. Save commit embedding vectors represented by CodeBERT
        3. Train feature extractors
        4. Infer feature extractors to extract commit's features
        5. Train neural classifier
        6. Apply adjustment function 
        7. Evaluate MiDas 

## Prerequisites
Make sure you create a directory to store embedding vectors, a folder "model" to store saved model, and a "features" folder to store extractor features following this hierarchy:
```
    VulPatchClassifier
        model
        features
        ...
    finetuned_embeddings
        variant_1
        variant_2
        variant_3
        variant_5
        variant_6
        variant_7
        variant_8
```

Note: If you run VulPatchClassifier on a Docker container, please run docker with parameters: "LANG=C.UTF-8 -e LC_ALL=C.UTF-8" to avoid error when writing to file, "--shm-size 16G" to avoid memory problem, "--gpus all" in case you use multiple GPUs

## Dataset
The dataset is available at: https://zenodo.org/record/5565182#.Yv3lHuxBxO8
Please download and put dataset inside the VulPatchClassifier folder


## Replication

Note: The current code base requires two GPUs to run. We will try to make it more flexible. 

#### Finetune CodeBERT
Corresponding to seven feature extractors, we have seven python scripts to finetune them.

| Feature extractor index | Finetuning script                     |
|------------------|---------------------------------------|
| 1                | python variant_1_finetune.py          |
| 2                | python variant_2_finetune.py          |
| 3                | python variant_3_finetune_separate.py |
| 5                | python variant_5_finetune.py          |
| 6                | python variant_6_finetune.py          |
| 7                | python variant_7_finetune_separate.py |
| 8                | python variant_8_finetune_separate.py |

#### Saving embedding vectors
After finetuning, run the following scripts to save embedding vectors corresponding to each feature extractor:

| Feature extractor index | Saving embeddings script                 |
|------------------|------------------------------------------|
| 1                | python preprocess_finetuned_variant_1.py |
| 2                | python preprocess_finetuned_variant_2.py |                    
| 3                | python preprocess_finetuned_variant_3.py |        
| 5                | python preprocess_finetuned_variant_5.py |           
| 6                | python preprocess_finetuned_variant_6.py |           
| 7                | python preprocess_finetuned_variant_7.py |  
| 8                | python preprocess_finetuned_variant_8.py |  

#### Saving embedding vectors 
Next, we need to train seven feature extractors

| Feature extractor index | Extractor training script                 |
|------------------|------------------------------------------|
| 1                | python variant_1.py |
| 2                | python variant_2.py |                    
| 3                | python variant_3.py |        
| 5                | python variant_5.py |           
| 6                | python variant_6.py |           
| 7                | python variant_7.py |  
| 8                | python variant_8.py |  


#### Infer feature extractors and train neural classifier

Simply use the following two commands:

```python3 feature_extractor_infer.py```

```python3 ensemble_classifier.py --model_path model/patch_ensemble_model.sav --java_result_path probs/prob_ensemble_classifier_test_java.txt --python_result_path probs/prob_ensemble_classifier_test_python.txt```


#### Apply adjustment function

Simply run:

```python adjustment_runner.py```

#### Evaluate MiDas

The script for evaluation is placed in evaluator.py

Run evaluator.py with parameter "--rq <rq_number>" to evaluate MiDas with the corresponding research questions:

**RQ1: Performance of MiDas on Java and Python project**

```python evaluator.py --rq 1```

**RQ2: Performance of MiDas with/without adjustment function**

```python evaluator.py --rq 2```

**RQ3: Performance of MiDas when continuously adding granularities**

To obtain performance of MiDas using only line level, run:

```python evaluator.py --rq 3 --mode 1```

To obtain performance of MiDas using only line level + hunk level, run two commands:

```python ensemble_classifier.py --ablation_study True -v 1 -v 2 -v 5 -v6  --model_path model/test_ablation_line_hunk_model.sav --java_result_path probs/test_ablation_line_hunk_java.txt --python_result_path probs/test_ablation_line_hunk_python.txt```

```python evaluator.py --rq 3 --mode 2```

To obtain performance of MiDas using only line level + hunk level + file level, run two commands:

```python ensemble_classifier.py --ablation_study True -v 1 -v 5  --model_path model/test_ablation_line_hunk_file_model.sav --java_result_path probs/test_ablation_line_hunk_file_java.txt --python_result_path probs/test_ablation_line_hunk_file_python.txt```

```python evaluator.py --rq 3 --mode 3```

The performance of MiDas using all granularities is obtained in RQ1 (we hope you run it successfully).


## Inference

To infer a commit, please use function *infer(commit, LOC)* in **inference.py**

Format: 
        Commit: list of code changes at file level
        LOC: Total lines of code change for the ease of using our adjustment function

        


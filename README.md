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
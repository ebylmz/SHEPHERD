# Dataset

## Preprocessing: 
1. Map genes to Ensembl IDs
2. Map phenotypes to the 2019 version of HPO
3. Output a jsonlines file where each json contains information for a single patient

See the data preprocessing [readme](https://github.com/mims-harvard/SHEPHERD/blob/main/data_prep/README.md)

The rare disease knowledge graph and patient datasets are provided in the appropriate format for SHEPHERD. If you would like to add your own set of patients, please adhere to the format used in the MyGene2 and simulated rare disease patients' files (see README in data_prep folder for more details).

- the file should be structured as a jsonlines file where each json contains information for a single patient.
- each json must contain at least:
	- patient ID ("id"),
	- a list of phenotypes present in the patient as HPO terms ("positive_phenotypes"),
	- list of causal genes as Ensembl IDs ("true_genes").
	
	
- To run causal gene discovery, the json must also include a list of all candidate genes as Ensembl IDs ("all_candidate_genes"). 
- To run novel disease characterization, the json must also include a list of true disease names as MONDO IDs ("true_diseases").

## Shortest Paths Calculations
Generate shortest paths calculations using the flag --only_test_data

```bash
python ../data_prep/shortest_paths/add_spl_to_patients.py \
    --save_prefix mygene2_5.7.22_head \
    --only_test_data
```


# Prediction

## Causal Gene Discovery

```bash
python predict.py \
--run_type causal_gene_discovery \
--patient_data my_data \
--edgelist KG_edgelist_mask.txt \
--node_map KG_node_map.txt \
--saved_node_embeddings_path /home/emirkan/projects/SHEPHERD/checkpoints/pretrain.ckpt \
--best_ckpt /home/emirkan/projects/SHEPHERD/checkpoints/causal_gene_discovery.ckpt 
```

# Results
The output of `predict.py` consists of:
- Dataframe of scores for each patient (`scores.csv`)
    Each patient's list of candidate genes are scored. The columns of the table are: patient ID, identifier of the candidate gene, similarity score, and binary correct label.
- Phenotype attention (`phenotype_attn.csv`)
- Patient embeddings (`phenotype_embeddings.pth`)
- *(Only for novel disease characterization)* Disease embeddings (`disease_embeddings.pth`)
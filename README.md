# GNTD
Graph-guided Neural Tensor Decomposition (GNTD) is program for reconstructing whole spatial transcriptomes from spatial gene expression profiling data such as the dataets generated by Visium ST and Stereo-seq. GNTD employs tensor structures and formulations to explicitly model the high-order spatial gene expression data with a hierarchical nonlinear decomposition in a three-layer neural network, enhanced by spatial relations among the capture spots and gene functional relations (in protein-protein interaction networks) for accurate reconstruction from highly sparse spatial profiling data.

![](https://github.com/kuanglab/GNTD/blob/main/GNTD_Workflow.png)

Installation and package requirements
--------------------------------------------------------------------
The python package can be downloaded and run with the following library versions.
```
[python 3.8.12]
[numpy 1.21.5]
[scipy 1.7.3]
[pandas 1.2.3]
[scikit-learn 1.1.3]
[pytorch 1.10.2]
[tensorly 0.6.0]
[scanpy 1.9.1]
[anndata 0.8.0]
```

Data preparation
--------------------------------------------------------------------------------

#### Spatial Transcriptomics Data
Download Visium spatial transcriptomics data from [10x Genomics](https://support.10xgenomics.com/spatial-gene-expression/datasets/) or [spatialLIBD](http://research.libd.org/globus/jhpce_HumanPilot10x/index.html) (require registration). We will use a mouse brain dataset as an example. 

Under Visium Demonstration (v1 Chemistry) choose tab "Space Ranger v1.1.0". Click the "Mouse Brain Section (Coronal)" link, fill in the form and check the consent to access the data. 

We only use filtered feature-barcode matrix data and spatial coordindates provided in the download file list. They can also be downloaded with following links [filtered feature-barcode matrix data](https://cf.10xgenomics.com/samples/spatial-exp/1.1.0/V1_Adult_Mouse_Brain/V1_Adult_Mouse_Brain_filtered_feature_bc_matrix.tar.gz) and [spatial coordinates](https://cf.10xgenomics.com/samples/spatial-exp/1.1.0/V1_Adult_Mouse_Brain/V1_Adult_Mouse_Brain_spatial.tar.gz). The data files are usually less than 100M. It takes less than one minute to download the files.

Then unzip the downloaded data and organize folders using the following structure under a home-folder for your experiment:

        . <data-folder>
        ├── ...
        ├── <tissue-folder>
        │   ├── filtered_feature_bc_matrix
        │   │   ├── barcodes.tsv.gz
        │   │   ├── features.tsv.gz
        │   │   └── matrix.mtx.gz
        │   ├── spatial
        │   │   └── tissue_positions_list.csv
        └── ...
        
#### Protein-protein Interaction Data
The current PPI networks can be downloaded from [BioGRID](https://thebiogrid.org/). This example uses mouse PPI network in [BioGRID version 4.4.209](https://downloads.thebiogrid.org/File/BioGRID/Release-Archive/BIOGRID-4.4.209/BIOGRID-ORGANISM-4.4.209.tab3.zip). Download and unzip the BIOGRID-ORGANISM-4.4.209.tab3.zip file and place the "BIOGRID-ORGANISM-<species>-4.4.209.tab3.txt" file under a folder as the PPI data (Please replace <species> with Mus_musculus to run the example).

        . <data-folder>
        ├── ...
        ├── BIOGRID-ORGANISM-<species>-4.4.209.tab3.txt
        └── ...

Running GNTD to get the imputed spatial transcriptomics data
--------------------------------------------------------------------------------
```python
from GNTD import GNTD
from preprocessing import preprocessing

raw_data_path = "<data-folder>/<tissue-folder>" # Path to spatial expression data
PPI_data_path = "<data-folder>/BIOGRID-ORGANISM-<species>-4.4.209.tab3.txt # Path to PPI data

rank = 128 # tensor rank
l = 0.1 # weight on graph regularization

expr_tensor, A_g, A_xy, ensembl_ids, gene_names, mapping = preprocessing(raw_data_path, PPI_data_path)
model = GNTD(expr_tensor, A_g, A_xy, rank, l)
expr_tensor_hat = model.impute()
```
Gene visualization in the imputed spatial transcriptomics data
--------------------------------------------------------------------------------

Clustering the imputed spatial transcriptomics data
--------------------------------------------------------------------------------


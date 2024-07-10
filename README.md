# Certificate of Reproducibility demonstrations

In this repository, demonstrations of data analysis utilizing Certificate of Reproducibility workflows are presented at multiple levels of complexity.

## Simple example: Leukopak/Ficoll comparison

**Notebook author:** Lucas T. Graybuck

This demonstration is stored in the `leukopak_pbmc_comparison/` subdirectory.

In this demo, we show a single analysis step downstream of data generated using our TEA-seq data processing pipeline. Here, we retrieve leukapheresis-derived batch control scRNA-seq data and data from a Ficoll PBMC sample, combine the data, and perform dimensionality reduction and a simple differential expression test between samples.

After performing this analysis, we assemble output files and deposit the results in HISE for storage of results, capture of the analysis environment, and later assembly of the results as a File Set that can be accessed by external users.

This analysis is performed in a single Jupyter Notebook:  
`leukopak_pbmc_comparison/compare_leukopak_pbmc_sample.ipynb`

## Multi-step example: Comparison of Pediatric and Adult TEA-seq data

**Notebook author:** Lucas T. Graybuck  
**Visualization author:** Lauren Okada

This demonstration is stored in the `adult_vs_pediatric_teaseq/` subdirectory. An interactive data visualization tool for differential gene expression is stored in the `dash_app/` subdirectory.

### Analysis of TEA-seq data

In this demo, we show a chain of analysis steps, in which each individual step is stored in HISE and used for subsequent analysis. The following steps are performed in the notebooks for this analysis:

- `00-R_select_samples.ipynb`: Identification of files and associated sample metadata stored in HISE for downstream analysis steps.
    - Input: HISE pipeline files and a query of HISE data storage
    - Output: A .csv file with the selected files and their metadata is stored for use in downstream steps.
- `01-R_get_h5_metadata.ipynb`: Assembly of the cell-level CITE-seq (RNA and ADT) metadata from the .h5 files generated as an output of the HISE TEA-seq pipeline.A .csv file with the selected files and their metadata is stored for use in downstream steps.
    - Input: sample metadata .csv file
    - Output: A single .csv file containing RNA and ADT metadata from all samples
- `02-R_get_arrow_metadata.ipynb`: Assembly of the cell-level ATAC-seq metadata from the .arrow files generated as an output of the HISE TEA-seq pipeline.
    - Input: sample metadata .csv file
    - A single .csv file containing ATAC-seq metadata from all samples
- `03-R_qc_filtering.ipynb`: RNA, ADT, and ATAC cell-level metadata are used to select cells that pass quality control criteria for all 3 modalities. Metadata for the selected cells is retained for downstream steps.
    - Input: sample metadata .csv, RNA/ADT cell metadata .csv, and ATAC cell metadata .csv
    - Output: A cell metadata .csv with only selected cells
- `04-R_t_cell_class_filtering.ipynb`: RNA, ADT, and ATAC-seq data are retrieved, and filtered cells are selected. We then use ADT cell surface marker expression to identify CD4 and CD8 T cells, and remove non-T cells from analysis.
    - Input: HISE pipeline files, filtered cell metadata.csv
    - Output: Seurat objects for RNA and ADT data for CD4 and CD8 T cells; .csv files with CD4 and CD8 T cell metadata
- `05-R_cd4_type_labeling.ipynb`: CD4 T cell data is labeled at a higher resolution by clustering and identification of cell type-specific marker expression.
    - Input: CD4 T cell Seurat object
    - Output: A .csv file with CD4 T cell type labels per cell
- `06-R_cd8_type_labeling.ipynb`: CD8 T cell data is labeled at a higher resolution by clustering and identification of cell type-specific marker expression.
    - Input: CD8 T cell Seurat object
    - Output: A .csv file with CD8 T cell type labels per cell
- `07-R_subject-downsample_MAST.ipynb`: CD4 and CD8 data is retrieved, labels are applied, and an equal number of cells is sampled from each subject. After sampling, MAST is used to identify differentially-expressed genes in comparisons between pediatric and older adult subjects.
    - Input: CD4 and CD8 T cell seurat objects; .csv files with CD4 and CD8 T cell labels
    - Output: a .csv file with MAST differential expression test results
- `08-R_assemble_pseudobulk_data.ipynb`: For visualization in a heatmap, this notebook was used to compute pseudobulk expression for each combination of sample and cell type.
    - Input: CD4 and CD8 T cell seurat objects; .csv files with CD4 and CD8 T cell labels
    - Output: .csv files with tables of pseudobulk expression; a .rds file containing lists of the same tables; and a .h5 file with the pseudobulk dataset formatted for fast retrieval by the visualization app.

### Visualization App

An interactive visualization app built on the Dash framework is available in the `adult_vs_pediatric_teaseq/dash_app/` directory.

Following assembly of differential gene expression and pseudobulk expression data, we generated an interactive visualization tool that enables exploration of these results. The following files are used for assembly of related data and app deployment:

- `01_GetHallmarkGenesetDescriptions.ipynb`: This notebook retrieves descriptions of Hallmark gene sets for display in the visualization app. Users are able to select Hallmark gene sets and highlight these genes in the volcano plot within the app.
- `02_FormatAppDat_updated.ipynb`: This notebook retrieves the data stored in HISE that were generated using the notebooks above (steps `07` and `08`), and structures the data for use and deployment with the visualization tool.
- `03_UploadVisualization.ipynb`: This notebook deploys the visualization tool to the Collaboration Space in HISE for our Certificate of Reproducibility demonstrations so that it can be connected to our public-facing HISE Publication.

The following files are utilized by the app itself:
- `app.py`: The main file containing code for the Dash visualization app.
- `assets/`: Contains additional CSS styles for the app.
- `data/`: This subdirectory contains files utilized by the app after deployment. Note that not all data is present in this repository - it must be retrieved and staged using the notebooks, above.

# Legal Information

## License

The license for this package is available on Github in the file LICENSE in this repository

## Level of Support

We are not currently supporting this code, but simply releasing it to the community AS IS but are not able to provide any guarantees of support. The community is welcome to submit issues, but you should not expect an active response.

## Contribution Agreement

If you contribute code to this repository through pull requests or other mechanisms, you are subject to the Allen Institute Contribution Agreement, which is available in the file CONTRIBUTING.md in this repository

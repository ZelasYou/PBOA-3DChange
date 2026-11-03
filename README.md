# PBOA-3DChange

**Object-oriented 3D building change detection from multi-temporal airborne LiDAR point clouds using intensity voxels and potential building-occupied areas.**

[简体中文](README.zh-CN.md)

## Release status

The code and experimental materials are being prepared for public release upon acceptance of the associated manuscript. This README describes the method, datasets, and planned reproducibility package. Installation instructions, executable entry points, dependency versions, and project-specific data download links will be added with the code release.

## Associated manuscript

**Object-oriented true 3D urban building change detection based on multi-temporal airborne LiDAR point clouds**

Liying Wang, Ze You, Siyu Lang, and Xinao Li  
Liaoning Technical University, Fuxin, China

Publication metadata and the final citation will be updated after acceptance.

## Overview

PBOA-3DChange represents airborne LiDAR point clouds as three-dimensional intensity voxels and identifies individual buildings through a potential building-occupied area (PBOA) model. Building objects extracted at different epochs are matched, and their geometric properties are compared to determine building change types.

The voxel representation preserves observed roof and façade geometry at different vertical levels. The information retained depends on the point cloud coverage and sampling; an observed voxel structure should not be interpreted as a complete reconstruction of every building surface.

## Processing workflow

1. **Prepare the input point clouds.** Use two epochs with a common coordinate reference system and consistent coordinate units. Check their spatial alignment and common study extent.
2. **Normalize intensity and match point density.** Apply histogram-based intensity normalization to a common range of 0–255. Downsample the higher-density point cloud to match the density of the lower-density point cloud before conducting the experiments.
3. **Construct intensity voxels.** Partition the point clouds using the selected voxel dimensions. Assign each occupied voxel the mean normalized intensity of its points, followed by the quantization used in the implementation. Record empty-voxel handling separately.
4. **Extract and individualize buildings.** Separate ground using spatial connectivity, detect ground voids associated with PBOAs, and use area and intensity criteria to filter non-building candidates.
5. **Match buildings and determine change types.** Match building objects across epochs and compare their geometric and bounding-box properties.
6. **Evaluate and visualize the results.** Compare predictions with reference annotations and generate object-level change maps and three-dimensional building visualizations.

The framework defines unchanged, newly constructed, demolished, height-increased, and height-decreased categories. The categories evaluated in a particular experiment depend on the available reference examples. The reported Tianjin experiments cover unchanged, newly constructed, and height-increased buildings; the Washington, D.C. experiments cover unchanged and height-increased buildings.

## Datasets

| Study area | Epochs | Study information | Access information |
|---|---|---|---|
| Tianjin, China | 2010 and 2016 | Urban airborne LiDAR point clouds with coordinates and return intensity | Dataset access instructions, reference annotations, and applicable sharing terms will be documented with the public release. |
| Washington, D.C., USA | 2015 and 2018 | DCGIS tile 1728; study extent of 800 m × 800 m | Original data are available through the official sources below. The release documentation will identify the exact files and preprocessing used in the experiments. |

### Washington, D.C. source data

The District of Columbia GIS program provides tiled LiDAR data for 2015 and 2018 through Amazon S3. Use these sources to locate the original data and accompanying metadata:

- [District of Columbia Classified Point Cloud LiDAR on the AWS Open Data Registry](https://registry.opendata.aws/dc-lidar/)
- [Official catalog entry for the 2015 and 2018 LiDAR download viewer](https://catalog.data.gov/dataset/viewer-to-download-2015-and-2018-lidar)
- [DCGIS download viewer](https://opendata.dc.gov/apps/DCGIS::viewer-to-download-2015-and-2018-lidar)

Follow the terms supplied by the original data provider. This project's software license will not replace the terms attached to third-party datasets.

### Input data and preprocessing records

The development workflow uses LAS point clouds containing X, Y, Z, and intensity. Dataset documentation accompanying the release will record:

- Source files, acquisition epochs, coordinate reference system, and coordinate units.
- Study boundary and any cropping or point filtering.
- Intensity normalization method and parameters.
- Point densities before and after sampling, the target density, the sampling method, and any random seed.
- Alignment checks, shared voxel dimensions, and voxel-grid positioning.
- Reference object identifiers, change labels, evaluation extent, and excluded objects.

Raw point counts and raw densities describe the source data. They must be distinguished from the input counts and matched densities used after preprocessing.

### Large data files

Large LAS files will be distributed through the original data portals or a separate data release, with access links recorded in the repository. Ordinary GitHub repositories block individual files larger than 100 MiB; see [GitHub's large-file documentation](https://docs.github.com/en/repositories/working-with-files/managing-large-files/about-large-files-on-github). The release will specify the selected distribution method and file checksums.

## Experimental settings

The following configurations are reported in the manuscript's voxel-resolution and neighborhood sensitivity analysis:

| Dataset | Selected voxel dimensions dx × dy × dz | Selected connectivity | Highest reported building detection quality Q |
|---|---|---|---|
| Tianjin | 1 m × 1 m × 0.3 m | 26-neighborhood | 97.1% |
| Washington, D.C. | 0.5 m × 0.5 m × 0.2 m | 26-neighborhood | 99.1% |

Three voxel scale factors, 0.5×, 1×, and 2× the estimated average point spacings, were evaluated with 6-, 18-, 26-, and 56-neighborhood connectivity. “Selected” refers to the best-performing tested configuration for each dataset.

The release configurations will also document the PBOA area threshold, intensity filtering parameters, building-matching threshold, height-change threshold, and preprocessing settings. These parameters should be read from the dataset-specific configuration rather than inferred from a visualization script.

## Evaluation

Building extraction and building change classification are evaluated separately. The reported metrics include:

- Completeness: `CP = TP / (TP + FN)`.
- Correctness: `CR = TP / (TP + FP)`.
- Quality: `Q = TP / (TP + FP + FN)`.

The evaluation documentation will specify the counting unit for each experiment, the reference annotations, and the matching criteria. A category with neither reference nor predicted samples has an undefined CP/CR and should be reported as not applicable. When a category has no reference samples but contains predicted objects, those predictions must be counted as false positives.

The percentages above are reference results reported in the manuscript. Reproduction requires the corresponding input data, preprocessing records, configurations, and reference annotations. Object-level detection or classification scores do not establish point-level geometric accuracy or performance in all urban environments.

The release documentation will also describe the building-level adaptation and settings of the INR-based comparison method. Third-party implementations and their attribution will be identified separately.

## Code and environment

The development materials include Python and MATLAB scripts. The Python scripts use libraries including NumPy, SciPy, laspy, PyVista, Polars, Shapely, OpenCV, Matplotlib, Seaborn, and tqdm, depending on the processing or visualization task. Some development scripts also depend on project-local modules.

A tested dependency specification, required MATLAB products where applicable, and the final executable entry points will accompany the public release. Installation and execution commands will be documented after the release package has been checked in a clean environment.

## Planned repository organization

The following layout is planned for the code release; it is not a statement that all directories are already populated.

```text
PBOA-3DChange/
├── README.md
├── README.zh-CN.md
├── src/                  # Processing and building change detection
├── configs/              # Dataset-specific parameters
├── examples/             # Small runnable examples
├── data/                 # Data access instructions and permitted sample data
├── evaluation/           # Reference-based evaluation
└── results/              # Example outputs and reproduction summaries
```

## Reproduction procedure

Once the release package is available:

1. Obtain the source code, dependency specification, and dataset-specific configurations.
2. Download the identified study data and corresponding reference annotations.
3. Prepare the environment and set local data locations using the documented configuration.
4. Apply the recorded preprocessing, including intensity normalization and point-density matching.
5. Run voxel construction, PBOA-based building extraction, inter-epoch matching, and change classification.
6. Evaluate building extraction and change classification using the documented units and reference extent.
7. Record the configuration, software version, hardware, processing time, and output files used for the comparison.

## Code and data availability

The source code and experimental materials will be made publicly available upon acceptance of the manuscript. The release will provide direct downloads for distributable project materials and precise source-data access instructions for externally hosted datasets. Access conditions for the Tianjin data will be stated explicitly.

## License

The software license will be provided with the public code release. Dataset terms will be documented separately, including the original terms for externally sourced data.

## Citation

Until final publication metadata are available, the associated manuscript may be cited as:

```bibtex
@unpublished{wang_pboa_3dchange,
  title  = {Object-oriented true 3D urban building change detection based on multi-temporal airborne LiDAR point clouds},
  author = {Wang, Liying and You, Ze and Lang, Siyu and Li, Xinao},
  note   = {Manuscript under review}
}
```

The citation will be updated with the publication year, journal details, and DOI when available.

## Contact

Corresponding author: **Liying Wang**  
Email: [wangliyinglntu@163.com](mailto:wangliyinglntu@163.com)

After the repository is published, please use its Issues tab for reproducibility questions and include the dataset, configuration, software version, and relevant error messages.

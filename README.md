# GraphAlignmentViewer:
Visualize the pileup of read alignments to a STR locus (or any path in the sequence graph) for one or more samples.
## Description:
  Genotyping STR loci is a difficult problem due to multiple reasons including flanks with similar genomic sequences, difficulty in aligning reads sequencing errors and sample contamination. ExpansionHunter is a tool that can automatically predict the genotypes of STR loci based on read alignments. However, due to the difficult nature of the problem, there may be errors in the predicted genotype. GraphAlignmentViewer, is a standalone Python3 script that creates a visualization of all read alignments to an STR locus to enable visual inspection of the genotype predictions made by ExpansionHunter. The script can visualize one or more samples, including trios to compare genotype calls in multiple samples. For each sample the script requires genotypes predicted by ExpansionHunter in BAM format for EH version 3 or YAML format for EH version 2.5.
**Note:** The current version only supports graph specifications consisting of linear paths and self-loops, but not branching nodes.

## Usage:
`python3 GraphAlignmentViewer.py --help`
### Use case 1: Single sample
`python3 GraphAlignmentViewer.py --read_align READ_ALIGN_FILE [--vcf VCF_FILE]`
### Use case 2: Comparison of genotypes from multiple samples
`python3 GraphAlignmentViewer.py --read_align_list READ_ALIGN_FILE_LIST`
## Requirements:
* Python3
* Matplotlib
* Pysam
* Numpy
* PyYAML (if visualizing output from versions older than v3)
## Inputs:

### Use case 1: Single sample
1. `READ_ALIGN_FILE`: Read alignment file generated be ExpansionHunter. BAM for v3, YAML for v2.5.
2. `VCF_FILE`: VCF output of genotype calls from ExpansionHunter
### Use case 2: Comparison of genotypes from multiple samples
1. `READ_ALIGN_FILE_LIST`: A 2/3 column text CSV file containing the list of EH output for all samples:
* Column1: Sample name,
* Column2: Read alignment from EH output similar to `READ_ALIGN_FILE`,
* Column3 (optional): VCF file from EH output

The pileups for the samples are shown in the order reported in the input file.  
**Note:** If the paths to the read alignment BAM/YAML and VCF files are not absolute, they are chosen relative to the location of the sample list file.


## Optional Arguments:
| Option | Argument | Default | Description |
|:--:|:--:|:--:|:--|
|`--file_format` | `FILE_FORMAT` | `v3` | Format of read alignments from EH. [`v3`: BAM, `v2.5`: YAML] |
|`--variant_catalog` | `VARIANT_CATALOG` | `variant_catalog.json` | Path to variant catalog json file used to run EH |
|`--locus_id` | `LOCUS_ID` | Plot pileups for all loci | Comma-separated list of locus IDs for which to plot pileup |
|`--greyscale` | `-`      | Nucleotides colored in IGV color scheme | Show nucleotides in greyscale: high quality match - black, low quality match - grey, mismatch - red |
|`--show_read_names` | `-` | Do not display read names | Display read names next to the read alignment |
|`--show_insertions` | `-` | Do not display inserted sequences | Display full sequences of insertions |
|`--output_prefix` | `OUTPUT_PREFIX` | `<FILENAME>_<LOCUS_ID>` where filename is basename of `READ_ALIGN_FILE` or `READ_ALIGN_FILE_LIST` | Prefix of output file |
|`--title_suffix` | `TITLE_SUFFIX` | "" | Suffix text to be appended to title of the plot |
|`--reference_fasta` | `REFERENCE_FASTA` | Represent flanks with 'N's | Indexed FASTA file for reference sequence |
|`--node_grouping` | `NODE_GROUPING` | `1` (group by leftmost repeat node) | Comma-separated list of node indices (left flank=`0`) to group and sort reads by genotype. `NONE`: sort reads only by position, `ALL`: group by all repeat nodes from left to right. |
|`--region_extension_length` | `REGION_EXTENSION_LENGTH` (`INT`) | `1000` | Size of nodes flanking the region structure used for generating the read alignments |
|`--region_extension_clip_length` | `REGION_EXTENSION_CLIP_LENGTH` (`INT`) | `20` | Number of basepairs of flanking regions to display. `-1`: Infer from maximum span of reads overlapping the locus. |


## Outputs:
The script produces 2 images per locus:
1. `<OUTPUT_PREFIX>_<LOCUS_ID>.png`
2. `<OUTPUT_PREFIX>_<LOCUS_ID>.pdf`

The output files are created in the current working directory unless `--output_prefix` argument is provided. Specifically:
* If `--output_prefix OUTPUT_PREFIX` is set
  * If `OUTPUT_PREFIX` is provided using the `--output_prefix` option then the output files will be produced in the current working directory with file names `<OUTPUT_PREFIX>_C9ORF72.pdf` and `<OUTPUT_PREFIX>_C9ORF72.png`. Here `OUTPUT_PREFIX` may contain an absolute or relative path or only basename of the ouput files.
* Use case 1: Single sample
  * If `READ_ALIGN_FILE` is `<PARENT_DIRECTORY>/sample.bam` and `LOCUS_ID` is `C9ORF72`, then the output files will be produced in the current working directory with file names `<CURRENT_WORKING_DIRECTORY>/sample_C9ORF72.pdf` and `<CURRENT_WORKING_DIRECTORY>/sample_C9ORF72.png`.
* Use case 2: Comparison of genotypes from multiple samples
  * If `READ_ALIGN_FILE_LIST` is `<PARENT_DIRECTORY>/sample.csv` and `LOCUS_ID` is `C9ORF72`, then the output files will be produced in the current working directory with file names `<CURRENT_WORKING_DIRECTORY>/samplelist_C9ORF72.pdf` and `<CURRENT_WORKING_DIRECTORY>/samplelist_C9ORF72.png`.

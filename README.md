# Hi-GDT : A Hi-C-based 3D gene domain analysis tool for analyzing local chromatin contacts in plants
  
![Picture1](https://github.com/user-attachments/assets/f7db1078-e67c-4ee4-9540-594578f8351e)

For any question about Hi-GDT, please contact miso5103@snu.ac.kr

Example and raw data files are available at https://zenodo.org/records/14728732

## Dependencies
python3 >= 3.8.

Required packages: subprocess, numpy, pickle, multiprocessing, PIL, functools, itertools, scipy

## Installation
Github install

```
git clone https://github.com/CDL-HongwooLee/Hi-GDT
```

# Quick start

Hi-GDT starts from .hic file generated from juicer (https://github.com/aidenlab/juicer/wiki)

1. ```HiGDT.py``` identifies single-gene domains and multigene domains from an input .hic file.
2. ```HiGDTdiff.py``` finds differential single-gene domains across two different Hi-C datasets.

(Optional) ```PileUpImage.py``` makes images.pkl file that includes resized Hi-C contact images of given regions (.bed).

## HiGDT.py

### Usage

```
usage: HiGDT.py [-h] -b BEDFILE -c CHROMSIZE -p PREFIX [-j JUICERTOOLS] [-hic HIC] [-n NORM] [--skip] [-i1 INFILE1] [-i2 INFILE2]
                [-o OUTDIR] [-bf BP_FRAG] [-f RESFILE] [-r1 RESOLUTION1] [-r2 RESOLUTION2] [-c1 CUTOFF1] [-c2 CUTOFF2]
                [--pvalue] [-l LCUT] [-m MAXMULTIN] [-d MAXDIST] [-t THREADS]

optional arguments:
  -h, --help       show this help message and exit
  -b BEDFILE       Path to the .bed file which includes genic information.
                   The 4th column should contain the label of each element.

  -c CHROMSIZE     Path to the .chrom.sizes file.

  -p PREFIX        Prefix for naming output files.

  -j JUICERTOOLS   Path to the juicer_tools.jar file. Not required if the "--skip" option is used.

  -hic HIC         Path to the .hic file. Not required if the "--skip" option is used.

  -n NORM          Normalization method. Choose one of ["NONE", "VC", "VC_SQRT", "KR", "SCALE"].
                   Not required if the "--skip" option is used.
                   Default = 'SCALE'.

  --skip           Use this flag to skip generating matrix.pkl file(s) from a .hic file.
                   You can use this flag if you've previously generated matrix.pkl file(s) using HiGDT.py.
                   if set, -i1 {matrix.pkl} [-i2 {matrix2.pkl}] option(s) is required for input file(s).

  -i1 INFILE1      Path to the input matrix.pkl file generated at resolution1 (r1).
                   By default, this file is generated at {outdir}/{prefix}.{resolution}{BP/FRAG}.matrix.pkl.

  -i2 INFILE2      Path to the input matrix.pkl file generated at resolution2 (r2).
                   Required only if r1 != r2.

  -o OUTDIR        Path to the output directory where whole output files will be written.
                   Default = './'.

  -bf BP_FRAG      The unit of resolution; basepair or restriction fragment.
                   Choose one of ["BP", "FRAG"].
                   Default = 'BP'.

  -f RESFILE       Path to the space-delimited whole-genome digestion file.
                   Required only for restriction fragment resolution analysis.
                   This file can be generated by Juicer utils.

  -r1 RESOLUTION1  Resolution for single-gene domain analysis.
                   r1 = 250 (BP) or r1 = 1 (FRAG) is recommended if you have enough sequencing depth.
                   Default = 250 (BP).

  -r2 RESOLUTION2  Resolution for multigene domain analysis.
                   r2 = 500 (BP) or r2 = 2 (FRAG) is recommended if you have enough sequencing depth.
                   Default = 500 (BP).

  -c1 CUTOFF1      Statistical cutoff value for single-gene domain analysis.
                   Default = 0.05.

  -c2 CUTOFF2      P-value cutoff for multi-gene domain analysis.
                   Default = 0.05.

  --pvalue         Using P-value instead of false discovery rate (fdr).
                   Default = False. (Use FDR)

  -l LCUT          The size cutoff (bp) of genes for the comparison.
                   Should be larger than (resolution x 4).
                   Default = 1000.

  -m MAXMULTIN     The maximum number of genes in a multi-gene domain.
                   Larger number may slow down the analysis.
                   Default = 10.

  -d MAXDIST       The maximum distance (bp) between the first and the last gene in a multi-gene domain.
                   Default = 50000.

  -t THREADS       Number of threads to use. Ideally equal to the number of chromosomes.
                   Default = 1.

```

### Example run

Using 250 bp resolution for single-gene domain identification, and 500 bp resolution for multigene domain identification (default).


```
python3 HiGDT.py -j juicer_tools.jar -hic Athaliana.hic -n SCALE -b Araport11_gene_protein_coding.1-genes.bed -c TAIR10_chr_all.chrom.sizes -o example -p Athaliana -bf BP -r1 250 -r2 500 -t 5
```


Or (if you generated matrix.pkl files before, the below is slightly faster command)


```
python3 HiGDT.py --skip -i1 example/Athaliana.250BP.matrix.pkl -i2 example/Athaliana.500BP.matrix.pkl -b Araport11_gene_protein_coding.1-genes.bed -c TAIR10_chr_all.chrom.sizes -o example -p Athaliana.v2 -bf BP -r1 250 -r2 500 -t 5
```

### Input data format

#### (-b) .bed file 
Bed file with 6 columns includes genomic information. Label for each row should be placed in column 4.

```
Chromosome  start  end  label(geneID)  .  strand

Chr1    3631    5899    AT1G01010       .       +
Chr1    6788    9130    AT1G01020       .       -
```

#### Matrix.pkl file
```HiGDT.py``` generates compressed .matrix.pkl files that includes dumped Hi-C matrix information in dictionary format.
A matrix.pkl file can be used as input for ```HiGDT.py``` and ```PileUpImage.py```

### Output data format
```HiGDT.py``` makes 8 (when r1==r2) or 10 (r1!=r2) files in the specified output directory.

#### Fragment.bed file
```HiGDT.py``` makes fragment.bed file at given resolution.

```
Chromosome  start  end  fragment_number

Chr1    1       250     1
Chr1    250     500     2
```

#### Single, Multi, MergedGeneDomain.txt
Output files contain HiGDT-identified gene domains.


SingleGeneDomain.txt


```
Chromosome  start  end  Labels(geneIDs)  Ngenes  strands pvalue qvalue
Chr1    3631    5899    AT1G01010    1    +    0.0034741208809897    0.018066995264745533
```


MultiGeneDomain.txt


```
Chromosome  start  end  Labels(geneIDs)  Ngenes  strands pvalues1-6 qvalue1-6
Chr1    72339    84946    AT1G01160:AT1G01170:AT1G01180:AT1G01190    4    +:-:+:-    3.6e-04	2.4e-05	1.3e-06	3.5e-05	1.3e-05	3.3e-06	5.2e-03 6.1e-04	1.2e-05	1.9e-04	9.8e-05	2.1e-05
```

#### Single, Multi, MergedGeneDomain.juicebox.bed
Modified output files for juicebox visualization.


## HiGDTdiff.py

### Usage

```
usage: HiGDTdiff.py [-h] -pkl1 PICKLE1 -pkl2 PICKLE2 -b BEDFILE -r REFILE -p PREFIX [-f FLANKING] [-l LCUT] [-fc FC] [-c CUTOFF] [--pvalue] [-o OUTDIR] [--unnorm]

optional arguments:
  -h, --help     show this help message and exit
  -pkl1 PICKLE1  Path to the matrix1.pkl file for the control sample.
                 By default, this file is generated by HiGDT.py and is located at {outdir}/{prefix}.{resolution}{BP/FRAG}.matrix.pkl.

  -pkl2 PICKLE2  Path to the matrix2.pkl file for the treat sample'
                 By default, this file is generated by HiGDT.py and is located at {outdir}/{prefix}.{resolution}{BP/FRAG}.matrix.pkl.

  -b BEDFILE     Path to the .bed file which includes genic information.
                 The 4th column should contain labels for each element.

  -r REFILE      Path to the fragment.bed file, which is composed of 'chromosome', 'fragment_start', 'fragment_end', 'fragment_number'.
                 The resolution of this file must match that of matrix.pkl files.
                 Both fixed intervals and restriction fragment intervals are allowed.
                 By default, this file is generated by HiGDT.py at {outdir}/{prefix}.{resolution}{BP/FRAG}.bed.

  -p PREFIX      Prefix for naming output files.

  -f FLANKING    The size of surrounding regions (bp) for the comparison.
                 The recommended range is 1000-3000 bp.
                 Default = 2000.

  -l LCUT        The size cutoff (bp) of genes for the comparison.
                 Should be larger than (resolution x 4).
                 Default = 1000.œ

  -fc FC         The fold chage cutoff for comparing contact frequencies within gene body and surrounding flanking regions.
                 The fold chage values of average contact frequency (CF) are calculated.
                 e.g. The value 0.1 indicates (Surrounding CF in treatment)/(Surrounding CF in control) < (1 - 0.1)
                      & (Gene body CF in control)/(Gene body CF in treatment) < (1 - 0.1)

  -c CUTOFF      The statistical cutoff value for comparing surrounding contact frequencies.
                 Default = 0.05.

  --pvalue       Using P-value instead of false discovery rate (fdr).
                 Default = False. (Use FDR)

  -o OUTDIR      Path to the output directory where all output files will be written.
                 Default = './'.

  --unnorm       Normalize o/e values by the sum of genome-wide contact frequencies within gene body or surrounding regions.
                 Default = False. (Do normalization)

```

### Example run

```
python3 HiGDTdiff.py -pk1 example/Control.250BP.matrix.pkl -pk2 example/Treat.250BP.matrix.pkl -b Araport11_gene_protein_coding.1-genes.bed -r example/Control.250BP.bed -f 2000 -l 1000 -fc 0.1 -c 0.05 -o example/ -p Athaliana_diff
```

### Input information
All input files except ```Araport11_gene_protein_coding.1-genes.bed``` file are produced from ```HiGDT.py```.

### Output data format
Output file is generated with the name of {prefix}.HiGDTdiff.out.txt.



```
GeneID  P-value  Q-value  FoldChange(surrounding contacts)  FoldChange(genebody contacts)  Domain_changes

AT1G01010    0.91    0.96    0.97    1.06    Not significant
...
AT1G01490    0.002    0.027    0.89    1.16    Insulated in treatment
...
AT1G01590    5.6e-05    2.3e-03    1.27    0.86    Insulated in control
...

```

'Insulated in treatment' indicates the insulation of a single-gene domain is enhanced in treat Hi-C data compared to control Hi-C data


## PileUpImage.py

### Usage

```
usage: PileUpImage.py [-h] -b BEDFILE -c CHROMSIZE -f REFILE -p PREFIX -r RESOLUTION [-j JUICERTOOLS] [-hic HIC] [-n NORM] [--skip] [-i INFILE] [-bf BP_FRAG] [-s IMGSIZE] [-bs BINSIZE] [-pad PADDING] [-o OUTDIR] [-t THREADS]

optional arguments:
  -h, --help      show this help message and exit
  -b BEDFILE      Path to the .bed file which includes specific regions for pileup analysis.
                  The 4th column should contain the label of each element.

  -c CHROMSIZE    Path to the .chrom.sizes file.

  -f REFILE       Path to the fragment.bed file which is composed of 'chromosome', 'fragment_start', 'fragment_end', 'fragment_number'.
                  The resolution of this file should match with that of matrix.pkl files.
                  Both fixed intervals and restriction fragment intervals can be used.
                  By default, this file is generated by HiGDT.py as {outdir}/{prefix}.{resolution}{BP/FRAG}.bed.

  -p PREFIX       Prefix for naming the output file.
                  {outdir}/{prefix}.pkl will be generated.

  -r RESOLUTION   Resolution of input matrix.

  -j JUICERTOOLS  Path to the juicer_tools.jar file. Not required if the "--skip" option is used.

  -hic HIC        Path to the .hic file. Not required if the "--skip" option is used.

  -n NORM         Normalization method. Choose one of ["NONE", "VC", "VC_SQRT", "KR", "SCALE"].
                  Not required if the "--skip" option is used.
                  Default = 'SCALE'.

  --skip          Use this flag to skip generating matrix.pkl file from a .hic file.
                  You can use this flag if you've previously generated matrix.pkl file using or PileupGeneDomain.py or HiGDT.py.
                  if set, -i {matrix.pickle} option is required for input file.

  -i INFILE       Path to the input matrix.pkl file.
                  By default, this file is generated as {outdir}/{prefix}.{resolution}{BP/FRAG}.matrix.pkl by PileupGeneDomain.py or HiGDT.py.

  -bf BP_FRAG     The unit of resolution; basepair or restriction fragment.
                  Choose one of ["BP", "FRAG"].
                  Default = 'BP'.

  -s IMGSIZE      The size of output image size.
                  Each regions specified in .bed file will be resized to n*n matrix.
                  Too high value may cause memory problem. Recommend (20 - 80).
                  Default = 80.

  -bs BINSIZE     Initial image resolution (bp).
                  The bin size of initial image before resizing.
                  Smaller value may cause memory problem and spend more time.
                  10 is fine in Arabidopsis analysis.
                  Default = 10.

  -pad PADDING    The padding size (compared to the size of target regions) to be visualized.
                  Up/downstream (padding * region_size) (bp) will be visualized as well as target_regions
                  The visualized window = padding * region_size * 2 + region_size.
                  Default = 0.5.

  -o OUTDIR       Path to the output directory where whole output files will be written.
                  Default = './'.

  -t THREADS      Number of threads to use. Ideally equal to the number of chromosomes.
                  Default = 1.

```

### Example run
To make pile-up analysis for whole genes (or specifies certain regions by changing .bed file). Recommend to analyze for whole-genome and post-process with ```PileUpImage.py``` output file.


```
python3 PileUpImage.py -j juicer_tools.jar -hic Athaliana.hic -n SCALE -r 250 -f example/Athaliana.250BP.bed -b Araport11_gene_protein_coding.1-genes.bed -c TAIR10_chr_all.chrom.sizes -o pileup -p Athaliana -t 5
```


Or (if you generated matrix.pkl files before, the below is slightly faster command).


```
python3 PileUpImage.py --skip -i example/Athaliana.250BP.matrix.pkl -bf BP -r 250 -f example/Athaliana.250BP.bed -b Araport11_gene_protein_coding.1-genes.bed -c TAIR10_chr_all.chrom.sizes -o pileup -p Athaliana -t 5
```

### Output data format
Output file is compressed dictionary file (.pickle).
The keys of the dictionary are Labels(geneIDs), and the values of the dictionary are resized Hi-C images of specified regions.

This file can be easily visualized by python code or jupyter notebook(strongly recommended!!).
The example codes visualizing piled-up images are attached (```PileupTest.ipynb```).

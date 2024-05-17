# Hi-GDT : A Hi-C-based 3D gene domain analysis tool for analyzing local chromatin contacts in _Arabidopsis_


For any question about Hi-GDT, please contact miso5103@snu.ac.kr

# Dependencies
python3 >= 3.8.

Required packages
subprocess, numpy, pickle, multiprocessing, PIL, functools, itertools, scipy


# Quick start

## Usage

```
usage: HiGDT.py [-h] -b BEDFILE -c CHROMSIZE -p PREFIX [-j JUICERTOOLS] [-hic HIC] [-n NORM]
                [--skip] [-i1 INFILE1] [-i2 INFILE2] [-o OUTDIR] [-bf BP_FRAG] [-f RESFILE]
                [-r1 RESOLUTION1] [-r2 RESOLUTION2] [-c1 CUTOFF1] [-c2 CUTOFF2] [-s MAXSIZE]
                [-d MAXDIST] [-t THREADS]

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

  --skip           Use this flag to skip generating matrix.pickle file(s) from a .hic file.
                   You can use this flag if you've previously generated matrix.pickle file(s) using HiGDT.py.
                   if set, -i1 {matrix.pickle} [-i2 {matrix2.pickle}] option(s) is required for input file(s).

  -i1 INFILE1      Path to the input matrix.pickle file generated at resolution1 (r1).
                   By default, this file is generated at {outdir}/{prefix}.{resolution}{BP/FRAG}.matrix.pickle.

  -i2 INFILE2      Path to the input matrix.pickle file generated at resolution2 (r2).
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

  -c1 CUTOFF1      P-value cutoff for single-gene domain analysis.
                   Default = 0.05.

  -c2 CUTOFF2      P-value cutoff for multi-gene domain analysis.
                   Default = 0.05.

  -s MAXSIZE       The maximum number of genes in a multi-gene domain.
                   Larger number may slow down the analysis.
                   Default = 10.

  -d MAXDIST       The maximum distance (bp) between the first and the last gene in a multi-gene domain.
                   Default = 50000.

  -t THREADS       Number of threads to use. Ideally equal to the number of chromosomes.
                   Default = 1.

```

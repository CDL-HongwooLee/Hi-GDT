# Hi-GDT : A Hi-C-based 3D gene domain analysis tool for analyzing local chromatin contacts in _Arabidopsis_


For any question about Hi-GDT, please contact miso5103@snu.ac.kr

## Dependencies
python3 >= 3.8.

Required packages: subprocess, numpy, pickle, multiprocessing, PIL, functools, itertools, scipy


# Quick start

## HiGDT.py

### Usage

```
usage: HiGDT.py [-h] -b BEDFILE -c CHROMSIZE -p PREFIX [-j JUICERTOOLS] [-hic HIC] [-n NORM] [--skip] [-i1 INFILE1] [-i2 INFILE2]
                [-o OUTDIR] [-bf BP_FRAG] [-f RESFILE] [-r1 RESOLUTION1] [-r2 RESOLUTION2] [-c1 CUTOFF1] [-c2 CUTOFF2] [-s MAXSIZE]
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
## HiGDTdiff.py

### Usage

```
usage: HiGDTdiff.py [-h] -pk1 PICKLE1 -pk2 PICKLE2 -gd1 GENEDOMAIN1 -gd2 GENEDOMAIN2 -b BEDFILE -r REFILE -p PREFIX [-f FLANKING] [-l LCUT] [-fc FC] [-c CUTOFF] [-o OUTDIR] [--norm]

optional arguments:
  -h, --help        show this help message and exit
  -pk1 PICKLE1      Path to the matrix1.pickle file for the control sample.
                    By default, this file is generated by HiGDT.py and is located at {outdir}/{prefix}.{resolution}{BP/FRAG}.matrix.pickle.

  -pk2 PICKLE2      Path to the matrix2.pickle file for the treat sample'
                    By default, this file is generated by HiGDT.py and is located at {outdir}/{prefix}.{resolution}{BP/FRAG}.matrix.pickle.

  -gd1 GENEDOMAIN1  Path to the HiGDT output file of the control sample including single-gene domains.
                    By default, this file is generated by HiGDT.py and is located at {outdir}/{prefix}.SingeGeneDomain.txt

  -gd2 GENEDOMAIN2  Path to the HiGDT output file of the treat sample including single-gene domains.
                    By default, this file is generated by HiGDT.py and is located at {outdir}/{prefix}.SingeGeneDomain.txt

  -b BEDFILE        Path to the .bed file which includes genic information.
                    The 4th column should contain labels for each element.

  -r REFILE         Path to the fragment.bed file, which is composed of 'chromosome', 'fragment_start', 'fragment_end', 'fragment_number'.
                    The resolution of this file must match that of matrix.pickle files.
                    Both fixed intervals and restriction fragment intervals are allowed.
                    By default, this file is generated by HiGDT.py at {outdir}/{prefix}.{resolution}{BP/FRAG}.bed.

  -p PREFIX         Prefix for naming output files.

  -f FLANKING       The size of surrounding regions (bp) for the comparison.
                    The recommended range is 1000-3000 bp.
                    Default = 2000.

  -l LCUT           The size cutoff (bp) of genes for the comparison.
                    Should be larger than (resolution x 4).
                    Default = 1000.

  -fc FC            The fold chage cutoff for comparing surrounding contact frequencies.
                    The fold chage of average contact frequencies (ACFs) within surrounding regions are calculated.
                    e.g. The value 0.2 means (Control ACFs) > (1-0.2) X (Treat ACFs).
                    Default = 0.2 (0 < FC < 1).

  -c CUTOFF         The P-value cutoff value for comparing surrounding contact frequencies.
                    Default = 0.05.

  -o OUTDIR         Path to the output directory where all output files will be written.
                    Default = './'.

  --norm            Normalize o/e values by the sum of genome-wide surrounding contact frequencies.
                    Default = False.

```

## PileUpImage.py

### Usage

```
usage: PileUpImage.py [-h] -b BEDFILE -c CHROMSIZE -f REFILE -p PREFIX -r RESOLUTION [-j JUICERTOOLS] [-hic HIC] [-n NORM] [--skip] [-i INFILE] [-bf BP_FRAG] [-s IMGSIZE] [-bs BINSIZE] [-pad PADDING] [-o OUTDIR]
                      [-t THREADS]

optional arguments:
  -h, --help      show this help message and exit
  -b BEDFILE      Path to the .bed file which includes specific regions for pileup analysis.
                  The 4th column should contain the label of each element.

  -c CHROMSIZE    Path to the .chrom.sizes file.

  -f REFILE       Path to the fragment.bed file which is composed of 'chromosome', 'fragment_start', 'fragment_end', 'fragment_number'.
                  The resolution of this file should match with that of matrix.pickle files.
                  Both fixed intervals and restriction fragment intervals can be used.
                  By default, this file is generated by HiGDT.py as {outdir}/{prefix}.{resolution}{BP/FRAG}.bed.

  -p PREFIX       Prefix for naming the output file.
                  {outdir}/{prefix}.pickle will be generated.

  -r RESOLUTION   Resolution of input matrix.

  -j JUICERTOOLS  Path to the juicer_tools.jar file. Not required if the "--skip" option is used.

  -hic HIC        Path to the .hic file. Not required if the "--skip" option is used.

  -n NORM         Normalization method. Choose one of ["NONE", "VC", "VC_SQRT", "KR", "SCALE"].
                  Not required if the "--skip" option is used.
                  Default = 'SCALE'.

  --skip          Use this flag to skip generating matrix.pickle file from a .hic file.
                  You can use this flag if you've previously generated matrix.pickle file using or PileupGeneDomain.py or HiGDT.py.
                  if set, -i {matrix.pickle} option is required for input file.

  -i INFILE       Path to the input matrix.pickle file.
                  By default, this file is generated as {outdir}/{prefix}.{resolution}{BP/FRAG}.matrix.pickle by PileupGeneDomain.py or HiGDT.py.

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

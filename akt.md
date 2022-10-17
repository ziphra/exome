- [akt — ancestry and kinship toolkit](#akt--ancestry-and-kinship-toolkit)
  - [parameters](#parameters)
    - [akt kin](#akt-kin)
  - [output table](#output-table)
  - [Relatedness and kinship](#relatedness-and-kinship)
  - [Practical examples](#practical-examples)
  - [SeqCap_EZ_MedExome_14122017](#seqcap_ez_medexome_14122017)
    - [first cousins parents](#first-cousins-parents)
      - [pedigree](#pedigree)
      - [kinship table](#kinship-table)
      - [`akt` pedigree](#akt-pedigree)
  - [KAPA_HyperExome_20052021_1](#kapa_hyperexome_20052021_1)
    - [half-siblings and half-uncle](#half-siblings-and-half-uncle)
      - [pedigree](#pedigree-1)
      - [kinship table](#kinship-table-1)
      - [`akt` pedigree](#akt-pedigree-1)
  - [24042020-1](#24042020-1)
    - [non-paternity and related parents](#non-paternity-and-related-parents)
      - [pedigree](#pedigree-2)
      - [kinship table](#kinship-table-2)
      - [`akt` pedigree](#akt-pedigree-2)
  - [KAPA-HyperExome_01062022-1](#kapa-hyperexome_01062022-1)
    - [Duplicats](#duplicats)
      - [pedigree](#pedigree-3)
      - [kinship table](#kinship-table-3)


# akt — ancestry and kinship toolkit

Estimating the proportion of the genome that is identical-by-descent (IBD) between two samples allows to estimate the degrees of relatedness between samples.

First, `akt` calculates (directly from multi-samples VCFs) IBD estimators and kinship coefficients - the probability that alleles sampled randomly from each individual are inherited by descent. 

## parameters 
### akt kin 
**Kinship calculation options:**   
  - `-k --minkin`: threshold for relatedness output (none)
  - `-F --freq-file`: a file containing population allele frequencies to use in kinship calculation. 
  Allele frequency is usually recquired to calculate IBD ans kinship estimators.   
  Estimation of IBD and kinship relie on the observed genotypes to infer the allelic identity by descent. These translations generally require allele frequencies from reference populations. A common estimation approach for large cohort, is to estimate allele frequency from the current population. However; this assumes no inbreeding and low mean relationship between individuals in the sample. (See [Goudet et al, 2018](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6220858/))
  - `-M --method`: type of estimator. `0`: plink (default) `1`: king-robust `2`: genetic-relationship-matrix. With default `-M 0`, kinship=0.5 x IBD2 + 0.25 x IBD1. `1` uses the robust kinship coefficent estimate described in the King paper. Assumption of population homogeneity leads to biased results, systematically inflating the degree of relatedness among individuals of the same racial group. 
  - `-a --aftag`: allele frequency tag (default AF)
  - `-@ --threads`: num threads


**Site filtering options**:
- `-R --regions-file`: restrict to regions listed in a file. Allow to use `akt` set of reliables and LD-pruned SNPs with their allele frequency in 1KG to conduct kinship calculation. `akt` preferred approach is to use a pre-determined well behaved sparse set of common SNPs.
- `-r --regions`: chromosome region
- `-T --targets-file`: similar to `-R` but streams rather than index-jumps
- `-t --targets`: similar to `-r` but streams rather than index-jumps
- `--force`: run kin without `-R/-T/-F`


## output table 
Output columns: 

- **IBD0**: the proportion of the genome with zero shared alleles
- **IBD1**: the proportion of teh genome with one shared alleles
- **IBD2**: the proportion of the genome with 2 shared alleles 
- **KINSHIP**: 0.5 x IBD2 + 0.25 x IBD1
- **NSNP**: the number of reliable SNPs used to estimate the previous estimators


ID1 | ID2 | IBD0 | IBD1 | IBD2 | KINSHIP | NSNP
---- | ---- | ---- | ---- | ---- | ---- | ----
XXX06 | XXX27 | 0.02131 | 0.93582 | 0.04288 |0.25539 |17633
XXX27 | XXX94 | 0.79447 | 0.20553 | 0.00000 | 0.05138 | 17635
XXX06 | XXX94 | 0.02535 | 0.94945 | 0.02520 | 0.24996 | 17647

## Relatedness and kinship 
- Monozygote twins share the same genome: the IBD2 will be 1 while IBD0 and IBD1 remain 0, and kinship would be 0.5.
- A parent and a child share half of their genomes, meaning IBD1 will be close to 1. While IBD0 and IBD2 stay close to 0, kinship will be around 0.25.
- ...

| relative pair          | IBD0,IBD1,IBD2 | kinship | relationship degree      | `akt` output |
|------------------------|----------------|---------|--------------------------|--------------|
| monozygote twins       | 0,0,1          | 0.5     |                          | `Dup`        |
| parent-child           | 0,1,0          | 0.25    | first-degree relatives   | `Parent-child `|
| siblings               | 0.25,0.5,0.25  | 0.25    | first-degree relatives   | `Siblings` |
| uncle-niece            | 0.5,0.5,0      | 0.125   | second-degree relatives  | `Second-order`|
| half-siblings          | 0.5,0.5,0      | 0.125   | second-degree relatives  | `Second-order`|
| grandparent-grandchild | 0.5,0.5,0      | 0.125   | second-degree relatives  | `Second-order` |
| cousin                 | 0.8,0.2,0      | 0.063   | third-degree relatives   | `Higher-order` |
| half-first cousin      |                | 0.031   | fourth-degree relatives  | |
| second cousin          |                | 0.016   | fifth-degree relatives   | |


## Practical examples
## SeqCap_EZ_MedExome_14122017
### first cousins parents 
#### pedigree

| Fam                    | Index      | Mother     | Father     | Comment               |
|------------------------|------------|------------|------------|-----------------------|
| EF17_223               | 17CY000610 | 17CY000612 | 17CY000611 | First cousins parents |
| EF17_593               | XXX06 | XXX94 | XXX27 | First cousins parents |


#### kinship table
```
XXX06 XXX27  0.02131 0.93582 0.04288 0.25539 17633
XXX06 XXX94  0.02535 0.94945 0.02520 0.24996 17647
XXX27 XXX94  0.79447 0.20553 0.00000 0.05138 17635
...
```

First cousins' relationship is a `higher order` relationship, with a kinship of around 0.063 (0.05138 here).   
Half-aunt, half-uncle/half-niece, half-nephew also share a higher order relationship, as well as great-grandparents and great-grandchilds...

#### `akt` pedigree
```
Fam3	XXX06
Fam3	XXX27
Fam3	XXX94
Type	Fam3	XXX27	XXX06	Parent/Child
Type	Fam3	XXX27	XXX94	Higher-order
Type	Fam3	XXX94	XXX06	Parent/Child
```


## KAPA_HyperExome_20052021_1
### half-siblings and half-uncle 
#### pedigree

| Fam      | Index        | Mother       | Father       | Comment                                      |
|----------|--------------|--------------|--------------|----------------------------------------------|
| EF18_725 | XXX487 | XXX488 | XXX489 | Quatuor                                    |
| EF18_725 | XXX490 |              |              | Quatuor. Oncle maternelle du CI XXX487 |

#### kinship table

```
XXX487 XXX488  0.01267 0.98733 0.00000 0.24683 18767
XXX487 XXX489  0.01267 0.98733 0.00000 0.24683 18768
XXX487 XXX490  0.80198 0.18780 0.01022 0.05206 18770
XXX488 XXX489  0.95087 0.04913 0.00000 0.01228 18752
XXX488 XXX490  0.55858 0.43166 0.00976 0.11280 18756
XXX489 XXX490  0.93955 0.06045 0.00000 0.01511 18758

```

As half-uncle/half-nephew, XXX487 and XXX490 share an higher order relationship (kinship = 0.05206).
As half-siblings, XXX490 and XXX488 share a second order relationship (kinship = 0.1228).

#### `akt` pedigree
```
Fam3	XXX487
Fam3	XXX488
Fam3	XXX489
Fam3	XXX490
Type	Fam3	XXX487	XXX490	Higher-order
Type	Fam3	XXX488	XXX487	Parent/Child
Type	Fam3	XXX488	XXX490	Second-order
Type	Fam3	XXX489	XXX487	Parent/Child
```

## 24042020-1
### non-paternity and related parents
#### pedigree 
| Fam      | Index        | Mother       | Father       | comment                              |
|----------|--------------|--------------|--------------|--------------------------------------|
| EF18_184 | XXX478   | XXX947   | XXX479   | false paternity and related parents  |

  

#### kinship table
```
XXX478 XXX947  0.02350 0.65791 0.31859 0.32377 18539
XXX479 XXX947  0.96351 0.00000 0.03649 0.01825 18534
XXX478 XXX479  1.00000 0.00000 0.00000 0.00000 18534
```

The alleged father, XXX479, shares no IBD segment with the index case XXX478, meaning XXX479 is not the father.    
The index case XXX478, however, shares more with his mother than with an actual sibling (kinship=0.324 > 0.25). Even though `atk` established a sibling relationship between the index and its mother, one can assume XXX478' mother and father are closely related.   
There is also a fourth or fifth-order relationship between XXX479 and the index. 

#### `akt` pedigree 
```
Fam0	XXX478
Fam0	XXX947
Type	Fam0	XXX478	XXX947	Sibling
```

## KAPA-HyperExome_01062022-1
### Duplicats

#### pedigree

```
Dup0	XXX646
Dup0	XXX680
Fam0	XXX646
```

#### kinship table 
```
6622CY000646 6622CY000680  0.00204 0.00612 0.99184 0.49745 17172
```
# akt — ancestry and kinship toolkit

## kinship table
Estimating the proportion of the genome that is identical-by-descent (IBD) between two samples allows to estimate the degrees of relatedness between samples.

First, `akt` calculates (directly from multi-samples VCFS) IBD estimators and the kinship coefficients - the probability that alleles sampled at random from each individual are inhrited by descent.

- **IBD0**: the proportion of the genome with zero shared alleles
- **IBD1**: the proportion of teh genome with one shared alleles
- **IBD2**: the proportion of the genome with 2 shared alleles 
- **KINSHIP**: 0.5 x IBD2 + 0.25 x IBD1
- **NSNP**: the number of reliable SNPs used to estimates the previous estimators

```
ID1 ID2 IBD0 IBD1 IBD2 KINSHIP NSNP
17CY001406 17CY001427  0.02131 0.93582 0.04288 0.25539 17633
17CY001427 17CY001494  0.79447 0.20553 0.00000 0.05138 17635
17CY001406 17CY001494  0.02535 0.94945 0.02520 0.24996 17647
```
### Examples 
- Monozygote twins shares the same genome: the IBD2 will be 1 while IBD0 and IBD1 remains 0 and kinship would be 0.5.
- A parent and a child share half of their genomes, so IBD1 will be close to 1, while IBD0 and IBD2 stay close to 0 and kinship, will be around 0.25.

| relative pair          | IBD0,IBD1,IBD2 | kinship |                          |
|------------------------|----------------|---------|--------------------------|
| monozygote twins       | 0,0,1          | 0.5     |                          |
| parent-child           | 0,1,0          | 0.25    | first-degree relatives   |
| siblings               | 0.25,0.5,0.25  | 0.25    | first-degree relatives   |
| uncle-niece            | 0.5,0.5,0      | 0.125   | second-degree relatives  |
| half-siblings          | 0.5,0.5,0      | 0.125   | second-degree relatives  |
| grandparent-grandchild | 0.5,0.5,0      | 0.125   | second-degree relatives  |
| cousin                 | 0.8,0.2,0      | 0.063   | third-degree relatives   |
| half-first cousin      |                | 0.031   | fourth-degree relatives  |
| second cousin          |                | 0.016   | fifth-degree relatives   |


## SeqCap_EZ_MedExome_14122017
### first cousins parents 
#### pedigree

| Fam                    | Index      | Mother     | Father     | Comment               |
|------------------------|------------|------------|------------|-----------------------|
| EF17_531               | 17CY001278 | 17CY001279 | 17CY001461 |                       |
| EF17_223               | 17CY000610 | 17CY000612 | 17CY000611 | First cousins parents |
| EF17_593               | 17CY001406 | 17CY001494 | 17CY001427 | First cousins parents |
| EF17_222               | 17CY000608 | 17CY000609 | 17CY000615 |                       |


#### `akt kin` output 
```
17CY001406 17CY001427  0.02131 0.93582 0.04288 0.25539 17633
17CY001406 17CY001494  0.02535 0.94945 0.02520 0.24996 17647
17CY001427 17CY001494  0.79447 0.20553 0.00000 0.05138 17635
...
```

First cousins relationship is an `higher order` realationship, with a kinship around 0.063.   
Half-aunt, half-uncle / half-niece, half-nephew also share an higher order relationship, as well as great-grandparents and great-grandchild...

#### `akt relatives output`: pedigree
```
Fam3	17CY001406
Fam3	17CY001427
Fam3	17CY001494
Type	Fam3	17CY001427	17CY001406	Parent/Child
Type	Fam3	17CY001427	17CY001494	Higher-order
Type	Fam3	17CY001494	17CY001406	Parent/Child
```


## KAPA_HyperExome_20052021_1
### half-siblings and half-uncle 
#### pedigree

| Fam      | Index        | Mother       | Father       | Comment                                      |
|----------|--------------|--------------|--------------|----------------------------------------------|
| EF20_154 | 6620CY000401 | 6620CY000402 |              |                                              |
| EF20_158 | 6620CY000414 | 6620CY000413 | 6620CY000415 |                                              |
| EF16_317 | 6620CY000421 | 16CY001367   | 16CY001455   |                                              |
| EF18_725 | 6620CY000487 | 6620CY000488 | 6620CY000489 | Quatuor                                    |
| EF18_725 | 6620CY000490 |              |              | Quatuor. Oncle maternelle du CI 6620CY000487 |

#### `akt kin` output

```
6620CY000487 6620CY000488  0.01267 0.98733 0.00000 0.24683 18767
6620CY000487 6620CY000489  0.01267 0.98733 0.00000 0.24683 18768
6620CY000487 6620CY000490  0.80198 0.18780 0.01022 0.05206 18770
6620CY000488 6620CY000489  0.95087 0.04913 0.00000 0.01228 18752
6620CY000488 6620CY000490  0.55858 0.43166 0.00976 0.11280 18756
6620CY000489 6620CY000490  0.93955 0.06045 0.00000 0.01511 18758

```

As half-uncle/half-nephew, 6620CY000487 and 6620CY000490 share an higher order relationship (kinship=0.063).
As half-siblings, 6620CY000490 and 6620CY000488 shares a second order relationship (kinship=0.125).

#### `akt relatives` output: pedigree
```
Fam3	6620CY000487
Fam3	6620CY000488
Fam3	6620CY000489
Fam3	6620CY000490
Type	Fam3	6620CY000487	6620CY000490	Higher-order
Type	Fam3	6620CY000488	6620CY000487	Parent/Child
Type	Fam3	6620CY000488	6620CY000490	Second-order
Type	Fam3	6620CY000489	6620CY000487	Parent/Child
```

## 24042020-1
### non-paternity and related parents
#### pedigree 
| Fam      | Index        | Mother       | Father       | comment                              |
|----------|--------------|--------------|--------------|--------------------------------------|
| EF17_671 | 17CY001506   | 17CY001508   | 17CY001507   |                                      |
| EF18_184 | 18CY000478   | 18CY000947   | 18CY000479   | false paternity and related parents  |
| EF19_237 | 6619CY000607 | 6619CY000608 | 6619CY000609 |                                      |
| EF19_232 | 6619CY000596 | 6619CY000598 | 6619CY000597 |                                      |
  

#### `akt kin` output
```
18CY000478 18CY000947  0.02350 0.65791 0.31859 0.32377 18539
18CY000479 18CY000947  0.96351 0.00000 0.03649 0.01825 18534
18CY000478 18CY000479  1.00000 0.00000 0.00000 0.00000 18534
```

The alleged father 18CY000479 shares no IBD segment with the index case 18CY000478: 18CY000479 is not the father.    
The index case 18CY000478 however shares more with his mother than with an actual siblings (kinship=0.324 > 0.25). Even though `atk` established a siblings relationship between the index and its mother, one can assumed 18CY000478' mother and father are closely related.   
There is also a fourth or fifth order relationship between 18CY000479 and the index. 

#### `akt relatives` output: pedigree 
```
Fam0	18CY000478
Fam0	18CY000947
Type	Fam0	18CY000478	18CY000947	Sibling
```

## KAPA-HyperExome_01062022-1
### Duplicats

```
Dup0	6622CY000646
Dup0	6622CY000680
Fam0	6622CY000646
Fam1	6622CY000582
Fam1	6622CY000583
Fam1	6622CY000654
Type	Fam1	6622CY000582	6622CY000654	Parent/Child
Type	Fam1	6622CY000583	6622CY000654	Parent/Child
```


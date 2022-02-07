# McMurchie UNIX Assignment

## Data Inspection

### Attributes of `fang_et_al_genotypes`

1. When in the Unix_Assignment directory, I started out by just observing the first ten lines of the code, the first two lines of code, the first single line of code, and then the last ten lines of code. I also used less to page through the data, making it easier to observe the SNP IDs.
```
head fang_et_al_genotypes.txt
head -n 2 fang_et_al_genotypes.txt
head -n 1 fang_et_al_genotypes.txt
tail fang_et_al_genotypes.txt
less fang_et_al_genotypes.txt
```
2. Next, I checked the number of lines, words, and bytes in the file.
```
wc fang_et_al_genotypes.txt
```
3. I then checked the number of columns in the file, using awk rather than counting. Although I suspected that the number of columns was the same in the header and in the rest of the file, I checked both.
```
awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt
grep -v "^#" fang_et_al_genotypes.txt | awk -F "\t" '{print NF; exit}'
```
4. Finally, I checked the file size with my preferred (human readable) method
```
du -h fang_et_al_genotypes.txt
```

By inspecting this file I learned that:

1. This file contains a header that is one line long. The header names the contents of each column, including sample ID (Sample_ID), what appears to be opertational taxonomic unit (]G_OTU), Group, and SNP ID. Following the one line header is SNP data, showing the potential base pairs for the SNPs.The end of the file appears to follow the same format as the rest of the file, excluding header.
2. There are 2783 lines, 2744038 words, and 11051939 characters (bytes) in this file. 
3. There are 986 columns throughout the file.
4. The file size is 6.6M.


### Attributes of `snp_position.txt`

1. I started by observing the first ten lines of the code, the first three lines of code and the first single line of code. and then the last ten lines of code. I also used less to page through the data, which allowed me to page through the data.
```
head snp_position.txt
head -n 3 snp_position.txt
head -n 1 snp_position.txt
tail snp_position.txt
less snp_position.txt
```
2. Next, I checked the number of lines, words, and bytes in the file.
```
wc snp_position.txt
```
3. I then checked the number of columns in the file using awk. While I suspected that the number of columns was the same in the header and in the rest of the file, I checked both, as I did with the fang_et_al_genotypes.txt file.
```
awk -F "\t" '{print NF; exit}' snp_position.txt
grep -v "^#" snp_position.txt | awk -F "\t" '{print NF; exit}'
```
4. Finally, I checked the file size with du
```
du -h snp_position.txt
```
By inspecting this file I learned that:

1. The header of this file consists of one line and lists the contents of the columns, including SNP_ID, cdv_marker_id, Chromosome, Position, alt_pos, mult_positions, amplicon, cdv_map_feature.name, gene, candidate/random, Genaissance_daa_id, Sequenom_daa_id, count_amplicons, count_cmf, and count_gene. The rest of the file, up until the end, contains the data for each of these SNPs, which appear to be listed by SNP_ID. Sometimes, the position given is approximate, and noted as such.   
2. There are 984 lines, 13198 words, and 82763 characters (bytes) in this file. 
3. There are 15 columns throughout the file.
4. The file size is 41K.

##Data Processing

###Maize Data

```
here is my snippet of code used for data processing
```

Here is my brief description of what this code does


###Teosinte Data

```
here is my snippet of code used for data processing
```

Here is my brief description of what this code does

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
3. I then checked the number of columns in the file, using awk rather than counting. Although I suspected that the number of columns was the same in the header and other parts of the file, I checked both.
```
awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt
tail -n +5 fang_et_al_genotypes.txt | awk -F "\t" '{print NF; exit}' 
```
4. Finally, I checked the file size with my preferred (human readable) method
```
du -h fang_et_al_genotypes.txt
```

By inspecting this file I learned that:

1. This file contains a header that is one line long. The header names the contents of each column, including sample ID (Sample_ID), what appears to be opertational taxonomic unit (]G_OTU), Group, and a list of SNP IDs. Following the one line header is SNP data, showing the potential base pairs for the SNPs. The end of the file appears to follow the same format as the rest of the file, excluding header.
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
tail -n +5 snp_position.txt | awk -F "\t" '{print NF; exit}' 
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

## Data Processing


Before processing the genotypes files, I did some initial processing on the SNP positions file (`snp_positions.txt`) to facilitate joining. 

First, I cut the SNP positions file so that it contained only SNP_ID, Chromosome, and Position, which are the three columns we wish to join to the genotype data.
```
cut -f 1,3,4 snp_position.txt > cut_snp_position.txt
sort -c -k1,1 cut_snp_position.txt
```
This cuts the SNP position file so we only have column 1 (SNP_ID), column 3 (Chromosome), column 4 (Position) and checks whether the file is sorted based on column 1 (SNP_ID). Since the file was not sorted, I had to sort it.
```
sort -k1,1 cut_snp_position.txt > sorted_snp_position.txt
sort -c -k1,1 sorted_snp_position.txt
less sorted_snp_position.txt
wc sorted_snp_position.txt
```
This sorts the cut SNP position file based on SNP ID and checks my work (it is now sorted based on column 1, SNP ID, and `less` enabled us to observe the file to make sure it looked as it should). With this file prepared for joining, I then moved on to working with the genotypes files. The `wc` command showed us that `sorted_snp_position.txt` has 984 lines, which will be useful for checking whether the `join` we will do later is successful.


### Maize Data

The first thing I did was observe the groups present in the `fang_et_al.txt` file using:
```
cut -f3 fang_et_al_genotypes.txt |sort | uniq
```
This allowed us to see the full list of groups present, allowing us to check our work in later steps.


To pull out only the maize genotypes from `fang_et_al.txt` I ran:
```
grep -E "(ZMMIL|ZMMLR|ZMMMR|Sample_ID)" fang_et_al_genotypes.txt > maize_genotypes.txt
grep "TRIPS" maize_genotypes.txt
less maize_genotypes.txt
```
This takes the rows with data from maize groups (ZMMIL, ZMMLR, and ZMMR), as well as the header (using Sample_ID, a phrase found only in the header row) to enter into the `maize_genotypes.txt` file.  I checked my work to make sure other groups were not imported as well by searching for the "TRIPS" group, which fortunately was not imported into the `maize_genotypes.txt` file. I also checked my work with `less` to view the resulting file. 


Next I transposed the maize genotypes file:
```
awk -f transpose.awk maize_genotypes.txt > transposed_maize_genotypes.txt
less transposed_maize_genotypes.txt
```
Here, I flipped the rows and columns, which will allow us to join this file (`transposed maize_genotypes.txt`) with the SNP positions file. SNP IDs are now in column 1, which I viewed using `less`.

Then I sorted the transposed maize genotypes file so that I could join it with the SNP positions file:
```
sort -k1,1 transposed_maize_genotypes.txt > sort_maize_genotypes.txt
sort -c -k1,1 sort_maize_genotypes.txt
wc sort_maize_genotypes.txt
```
This sorted the transposed maize genotypes file based on the first column, which contains SNP ID, and checked the resulting file to make sure that the sort worked. The `sort_maize_genotypes.txt` file has 986 lines, which will be useful to help us check whether the join is successful.

I then joined the sorted maize genotypes file and the cut, sorted SNP positions file:
```
join -1 1 -2 1 sorted_snp_position.txt sort_maize_genotypes.txt > join_maize.txt
head join_maize.txt
less join_maize.txt
wc join_maize.txt
```
To make sure that the join was successful, I viewed the beginning of the file with `head` and used `less` to scroll through it. The `wc` command shows us that the `join_maize.txt` file has 983 lines, slightly shorter than `sorted_snp_position.txt` and `sort_maize_genotypes.txt` because the line containing "SNP_ID" is not copied over from `sorted_snp_position.txt` and the lines comtianing "Sample_ID", "]G_OTU", and "Group" are not copied over from `sort_maize_genotypes.txt`.


To prepare to make the 10 files with SNP ordered based on increasing position value, I sorted the joined maize SNP position and data file based on chromosome number first and position second:
```
sort -k2,2n -k3,3n join_maize.txt > all_chrom_maize.txt
less all_chrom_maize.txt
```
This sorts the file in ascending order based first on chromosome number (column 2) and then position number (column 3), with option "-n" ensuring that we're sorting numerically, rather than alpha-numerically. The `less` command was helpful here to view the file and make sure that the positions were sorted correctly.

Now we're ready to make the 10 files (1 for each maize chromosome) with SNPs ordered based on increasing position values. The missing data are already encoded with "?" at this point. To do this, I copied the data for each maize chromosome over to an assigned file.
```
awk '$2 ~ /^1$/' all_chrom_maize.txt > chr1_maize_question.txt | awk '$2 ~ /^2$/' all_chrom_maize.txt > chr2_maize_question.txt | awk '$2 ~ /^3$/' all_chrom_maize.txt > chr3_maize_question.txt | awk '$2 ~ /^4$/' all_chrom_maize.txt > chr4_maize_question.txt | awk '$2 ~ /^5$/' all_chrom_maize.txt > chr5_maize_question.txt | awk '$2 ~ /^6$/' all_chrom_maize.txt > chr6_maize_question.txt | awk '$2 ~ /^7$/' all_chrom_maize.txt > chr7_maize_question.txt | awk '$2 ~ /^8$/' all_chrom_maize.txt > chr8_maize_question.txt | awk '$2 ~ /^9$/' all_chrom_maize.txt > chr9_maize_question.txt | awk '$2 ~ /^10$/' all_chrom_maize.txt > chr10_maize_question.txt
```
Here, awk is taking the data associated from column 2 (chromosome), with the specific chromosome we want for each file between the slashes. The ^ and $ around the desired chromosome number indicate the beginning and end of the number (this prevents us from accidentally grabbing chromosome 10 along with chromosome 1). I viewed each of these files with `less`, `head`, and `tail` to make sure that they contained only data from the correct chromosome and in the right order. 

We end up with 10 final files, one for each maize chromosome, with SNPs ordered by increasing position and missing data encoded with "?": 
1. `chr1_maize_question.txt`
2. `chr2_maize_question.txt`
3. `chr3_maize_question.txt`
4. `chr4_maize_question.txt`
5. `chr5_maize_question.txt`
6. `chr6_maize_question.txt`
7. `chr7_maize_question.txt`
8. `chr8_maize_question.txt`
9. `chr9_maize_question.txt`
10. `chr10_maize_question.txt`

Since I wanted to make the next ten files (one for each chromosome), which require that SNPs are ordered based on decreasing position values and missing data encoded by "-", I had to resort the joined maize file and use sed to replace the "?" with a "-" symbol:
```
sort -k2,2n -k3,3nr join_maize.txt > all_chrom_maize_rev.txt
sed 's/?/-/g' all_chrom_maize_rev.txt > all_chrom_maize_dash.txt
less all_chrom_maize_dash.txt
```
The sort step sorts the file in ascending order based first on chromosome number (column 2) and then descending order for position number (column 3), with option "-nr" ensuring that we're sorting numerically, rather than alpha-numerically, and in reverse order. The sed step replaces the "?" used for missing data with a "-", which (along with order) I checked with `less`.

At this point, we're ready to make the 10 files (1 for each maize chromosome) with SNPs ordered based on decreasing position values, with missing data encoded with a "-". To do this, I copied the data for each maize chromosome over to an assigned file.
```
awk '$2 ~ /^1$/' all_chrom_maize_dash.txt > chr1_maize_dash.txt | awk '$2 ~ /^2$/' all_chrom_maize_dash.txt > chr2_maize_dash.txt | awk '$2 ~ /^3$/' all_chrom_maize_dash.txt > chr3_maize_dash.txt | awk '$2 ~ /^4$/' all_chrom_maize_dash.txt > chr4_maize_dash.txt | awk '$2 ~ /^5$/' all_chrom_maize_dash.txt > chr5_maize_dash.txt | awk '$2 ~ /^6$/' all_chrom_maize_dash.txt > chr6_maize_dash.txt | awk '$2 ~ /^7$/' all_chrom_maize_dash.txt > chr7_maize_dash.txt | awk '$2 ~ /^8$/' all_chrom_maize_dash.txt > chr8_maize_dash.txt | awk '$2 ~ /^9$/' all_chrom_maize_dash.txt > chr9_maize_dash.txt | awk '$2 ~ /^10$/' all_chrom_maize_dash.txt > chr10_maize_dash.txt
```
As before, awk is taking the data associated from column 2 (chromosome), with the specific chromosome we want for each file between the slashes. The ^ and $ around the desired chromosome number indicate the beginning and end of the number (this prevents us from accidentally grabbing chromosome 10 along with chromosome 1). Once again, I viewed the files with `less`, `head`, and `tail` to make sure that they contained only data from the correct chromosome and in the right order.


After doing this, we end up with 10 final files, one for each maize chromosome, with SNPs ordered by decreasing position and missing data encoded with "-": 
1. `chr1_maize_dash.txt`
2. `chr2_maize_dash.txt`
3. `chr3_maize_dash.txt`
4. `chr4_maize_dash.txt`
5. `chr5_maize_dash.txt`
6. `chr6_maize_dash.txt`
7. `chr7_maize_dash.txt`
8. `chr8_maize_dash.txt`
9. `chr9_maize_dash.txt`
10. `chr10_maize_dash.txt`

To put all the maize SNPs with unknown position in the genome into a single file, I simply ran: 
```
awk '$3 ~ /^unknown$/' all_chrom_maize_dash.txt > unknown_maize_snp.txt
less unknown_maize_snp.txt
```

This gives us one file with maize SNPs in unknown posiitons in the genome, which I checked with `less`:
1. `unknown_maize_snp.txt`

To put all the SNPs with multiple positions in the genome into a single file for maize, I ran: 
```
awk '$3 ~ /^multiple$/' all_chrom_maize_dash.txt > multiple_maize_snp.txt
less multiple_maize_snp.txt
```

This gives us one file with SNPs in multiple positions in the genome for maize, which I viewed with `less` to check:
1. `multiple_maize_snp.txt`

### Teosinte Data

The first I pulled out only the teosinte genotypes from `fang_et_al.txt`:
```
grep -E "(ZMPBA|ZMPIL|ZMPJA|Sample_ID)" fang_et_al_genotypes.txt > teosinte_genotypes.txt
grep "TRIPS" teosinte_genotypes.txt
less teosinte_genotypes.txt
```
This takes the rows with data from teosinte groups (ZMPBA, ZMPIL, and ZMPJA, as well as the header (using Sample_ID, a phrase found only in the header row) to enter into the `teosinte_genotypes.txt` file. I searched for a Tripsacum group in the `teosinte_genotypes.txt` file to make sure that it had not been accidentally imported (it had not). I also viewed `teosinte_genotypes.txt` with `less` to check that it looked correct.

Then I transposed the teosinte genotypes file:
```
awk -f transpose.awk teosinte_genotypes.txt > transposed_teosinte_genotypes.txt
less transposed_teosinte_genotypes.txt
```
This flips the rows and columns, allowing us to join this file (`transposed_teosinte_genotypes.txt`) with the SNP positions file. SNP IDs are now in column 1, which I viewed with `less`.

Next I sorted the transposed teosinte genotypes file so that I could join it with the SNP positions file:
```
sort -k1,1 transposed_teosinte_genotypes.txt > sort_teosinte_genotypes.txt
sort -c -k1,1 sort_teosinte_genotypes.txt
wc sort_teosinte_genotypes.txt
```
This sorted the transposed teosinte genotypes file based on the first column, which contains SNP ID, and checked that the sort was successful. The `wc` command shows us that there are 986 lines in the `sort_teosinte_genotypes.txt` file.

I then joined the sorted teosinte genotypes file and the cut, sorted SNP positions file:
```
join -1 1 -2 1 sorted_snp_position.txt sort_teosinte_genotypes.txt > join_teosinte.txt
head join_teosinte.txt
less join_teosinte.txt
wc join_teosinte.txt
```
Since this was a tricky step, I used both `head` and `less` to view my work here, as well as using `wc` to view the resulting number of lines. There are 983 lines in the `join_teosinte.txt` file, slightly shorter than `sorted_snp_positi
on.txt` and `sort_teosinte_genotypes.txt` because the line containing "SNP_ID" is not copied over from `sorted_snp_position.txt` and the lines comtianing "Sample_ID", "]G_OTU", and "Group" are not copied over from `sort_teosinte_genotypes.txt`.



Then I sorted the joined teosinte SNP position and data file based on chromosome number first and position second, checking my work with `less`, as I did with the maize file:
```
sort -k2,2n -k3,3n join_teosinte.txt > all_chrom_teosinte.txt
less all_chrom_teosinte.txt
```
This sorts the file in ascending order based first on chromosome number (column 2) and then position number (column 3). Option "-n" ensures that we sort numerically, rather than alpha-numerically. 

We're ready to make the 10 files (1 for each teosinte chromosome) with SNPs ordered based on increasing position values, just like we did with the maize files. The missing data are already encoded with "?" at this point. Once again I copied the data for each chromosome (this time for teosinte chromosomes) over to an assigned file.
```
awk '$2 ~ /^1$/' all_chrom_teosinte.txt > chr1_teosinte_question.txt | awk '$2 ~ /^2$/' all_chrom_teosinte.txt > chr2_teosinte_question.txt | awk '$2 ~ /^3$/' all_chrom_teosinte.txt > chr3_teosinte_question.txt | awk '$2 ~ /^4$/' all_chrom_teosinte.txt > chr4_teosinte_question.txt | awk '$2 ~ /^5$/' all_chrom_teosinte.txt > chr5_teosinte_question.txt | awk '$2 ~ /^6$/' all_chrom_teosinte.txt > chr6_teosinte_question.txt | awk '$2 ~ /^7$/' all_chrom_teosinte.txt > chr7_teosinte_question.txt | awk '$2 ~ /^8$/' all_chrom_teosinte.txt > chr8_teosinte_question.txt | awk '$2 ~ /^9$/' all_chrom_teosinte.txt > chr9_teosinte_question.txt | awk '$2 ~ /^10$/' all_chrom_teosinte.txt > chr10_teosinte_question.txt
```
Just like with the maize files, awk is taking the data associated from column 2 (chromosome), with the specific chromosome we want for each file between the slashes. The ^ and $ around the desired chromosome number indicate the beginning and end of the number (this prevents us from accidentally grabbing chromosome 10 along with chromosome 1). I checked each of the files with `head`, `tail`, and `less` to make sure that the only contained data from the desired chromosome.

We end up with 10 final files, one for each teosinte chromosome, with SNPs ordered by increasing position and missing data encoded with "?" here: 
1. `chr1_teosinte_question.txt`
2. `chr2_teosinte_question.txt`
3. `chr3_teosinte_question.txt`
4. `chr4_teosinte_question.txt`
5. `chr5_teosinte_question.txt`
6. `chr6_teosinte_question.txt`
7. `chr7_teosinte_question.txt`
8. `chr8_teosinte_question.txt`
9. `chr9_teosinte_question.txt`
10. `chr10_teosinte_question.txt`

To make the next ten files (one for each chromosome), which require that SNPs are ordered based on decreasing position values and missing data encoded by "-", I had to resort the joined teosinte file and use sed to replace the "?" with a "-" symbol, just like I did with the maize files, using `less` to make sure that things worked properly:
```
sort -k2,2n -k3,3nr join_teosinte.txt > all_chrom_teosinte_rev.txt
sed 's/?/-/g' all_chrom_teosinte_rev.txt > all_chrom_teosinte_dash.txt
less all_chrom_teosinte_dash.txt
```
In the sort step, I sorted the file in ascending order based first on chromosome number (column 2) and then descending order based on position number (column 3), with option "-nr" ensuring that we're sorting numerically, rather than alpha-numerically, and in reverse order, as I did with the maize files. The sed step replaces the "?" used for missing data with a "-".

At this point, we're ready to make the 10 files (1 for each teosinte chromosome) with SNPs ordered based on decreasing position values, with missing data encoded with a "-". To do this, I copied the data for each teosinte chromosome over to an assigned file, like I did with the maize chromosomes.
```
awk '$2 ~ /^1$/' all_chrom_teosinte_dash.txt > chr1_teosinte_dash.txt | awk '$2 ~ /^2$/' all_chrom_teosinte_dash.txt > chr2_teosinte_dash.txt | awk '$2 ~ /^3$/' all_chrom_teosinte_dash.txt > chr3_teosinte_dash.txt | awk '$2 ~ /^4$/' all_chrom_teosinte_dash.txt > chr4_teosinte_dash.txt | awk '$2 ~ /^5$/' all_chrom_teosinte_dash.txt > chr5_teosinte_dash.txt | awk '$2 ~ /^6$/' all_chrom_teosinte_dash.txt > chr6_teosinte_dash.txt | awk '$2 ~ /^7$/' all_chrom_teosinte_dash.txt > chr7_teosinte_dash.txt | awk '$2 ~ /^8$/' all_chrom_teosinte_dash.txt > chr8_teosinte_dash.txt | awk '$2 ~ /^9$/' all_chrom_teosinte_dash.txt > chr9_teosinte_dash.txt | awk '$2 ~ /^10$/' all_chrom_teosinte_dash.txt > chr10_teosinte_dash.txt
```
Once again, awk is taking the data associated from column 2 (chromosome), with the specific chromosome we want for each file between the slashes. The ^ and $ around the desired chromosome number indicate the beginning and end of the number (this prevents us from accidentally grabbing chromosome 10 along with chromosome 1). I used `head`, `tail`, and `less` to make sure I only had data from the right chromosome in each file.

After doing this, we end up with 10 final files, one for each teosinte chromosome, with SNPs ordered by decreasing position and missing data encoded with "-": 
1. `chr1_teosinte_dash.txt`
2. `chr2_teosinte_dash.txt`
3. `chr3_teosinte_dash.txt`
4. `chr4_teosinte_dash.txt`
5. `chr5_teosinte_dash.txt`
6. `chr6_teosinte_dash.txt`
7. `chr7_teosinte_dash.txt`
8. `chr8_teosinte_dash.txt`
9. `chr9_teosinte_dash.txt`
10. `chr10_teosinte_dash.txt`

To put all the teosinte SNPs with unknown position in the genome into a single file, I ran the following, using `less` to check my work: 
```
awk '$3 ~ /^unknown$/' all_chrom_teosinte_dash.txt > unknown_teosinte_snp.txt
less unknown_teosinte_snp.txt
```

This gives us one file with teosinte SNPs in unknown positions in the genome:
1. `unknown_teosinte_snp.txt`

To put all the SNPs with multiple positions in the genome into a single file for teosinte, I ran the following, using `less` to check my work: 
```
awk '$3 ~ /^multiple$/' all_chrom_teosinte_dash.txt > multiple_teosinte_snp.txt
less multiple_teosinte_snp.txt
```

This gives us one file with SNPs in multiple posiitons in the genome for teosinte:
1. `multiple_teosinte_snp.txt`


### An Organizational Note
I move intermediate files to the `intermediate_files` directory and final files to the `final_files` directory to facilitate viewing.

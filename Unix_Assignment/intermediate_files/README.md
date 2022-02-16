## Elizabeth McMurchie UNIX Assignment Intermediate files

### The following file was given:

* The `transpose.awk` script will be needed to transpose the data (see instructions in `UNIX_Assignment.md`).

### I have added the following files to this directory:

* `cut_snp_position.txt`: This file is based on `snp_position.txt` but contains only column 1 (SNP_ID), column 3 (Chromosome), column 4 (Position).
* `sorted_snp_position.txt`: This file is based on `cut_snp_position.txt`, but is sorted based on SNP_ID to facilitate joining.
* `maize_genotypes.txt`: This file contains genotypes of only maize groups.
* `transposed_maize_genotypes.txt`: This file contains the transposed maize genotypes data.
* `sort_maize_genotypes.txt`: This file contains the transposed maize genotypes data sorted by SNP ID so they can be joined with the SNP positions file.
* `join_maize.txt`: This file contains the joined maize genotypes data and SNP position data.
* `all_chrom_maize.txt`: This file is based on `join_maize.txt`, but is sorted with chromosome number and SNP position ordered based on increasing value.
* `all_chrom_maize_rev.txt`: Similar to `all_chrom_maize.txt`, but with SNP position ordered based on decreasing value.
* `all_chrom_maize_dash.txt`: The same as `all_chrom_maize_rev.txt`, but with the "?" for missing data replaced with a "-".

* `teosinte_genotypes.txt`: This file contains genotypes of only teosinte groups.
* `transposed_teosinte_genotypes.txt`: This file contains the transposed teosinte genotypes data.
* `sort_teosinte_genotypes.txt`: This file contains the transposed teosinte genotypes data sorted by SNP ID so they can be joined with the SNP positions file.
* `join_teosinte.txt`: This file contains the joined teosinte genotypes data and SNP position data.
* `all_chrom_teosinte.txt`: This file is based on `join_teosinte.txt`, but is sorted with chromosome number and SNP position ordered based on increasing value.
* `all_chrom_teosinte_rev.txt`: Similar to `all_chrom_teosinte.txt`, but with SNP position ordered based on decreasing value.
* `all_chrom_teosinte_dash.txt`: The same as `all_chrom_teosinte_rev.txt`, but with the "?" for missing data replaced with a "-".

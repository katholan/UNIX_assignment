#UNIX_assignment
Katerina Holan - UNIX assignment for EEOB 546X

##Data Inspection
###fang\_et\_al_genotypes.txt file

1. head fang\_et\_al_genotypes.txt
  * file has too many columns to properly view
2. cut -f1-10 fang\_et\_al_genotypes.txt | head
  * made it much easier to get a better sense of the data.  Genes are the columns and SNPs/SNP locations are the rows
3. head -n 1 fang\_et\_al_genotypes.txt | wc
  * 986 total columns
4. cut -f1 fang\_et\_al_genotypes.txt | wc
  * 2783 total rows
5. du -h fang\_et\_al_genotypes.txt
  * file is 11Mb
  
###snp_position.txt
1. head snp_position.txt
  * hard to see what header goes with which column
2. head snp_position.txt | column -t
  * outputs an easier to understand table
3. head -n 1 snp_position.txt | wc
  * 15 columns
4. cut -f1 snp_position.txt | wc
  * 984 rows
  * this matches up with the fang file since there are 3 extra description columns in addition to the gene columns and in the snp file there is only one column of description

##Data Processing
###Pull out Maize/Teosinte Genotypes, transpose
1. cut -f3 fang\_et\_al_genotypes.txt | sort | uniq -c
  * pulls 3rd column, sorts the genotypes, and then counts the number of unique inputs.  
  * maize:  ZMMIL = 290, ZMMLR = 1256, ZMMMR = 27, total = 1573
  * teosinte:  ZMPBA = 900, ZMPIL = 41, ZMPJA = 34, total = 975
2. grep 'ZMM' fang\_et\_al_genotypes.txt > maize_genotypes.txt 
  * outputs all rows of fang that are maize genotypes to a new file called maize_genotypes.txt
  * this will NOT include the header rows so I still need to add those
  * cut -f1 maize_genotypes.txt | wc outputted 1573 rows which means I got all of the maize rows
3. grep 'ZMP' fang_et_al_genotypes.txt > teosinte_genotypes.txt
  * outputs teosinte genotypes to teosinte_genotypes.txt
  * WON'T include header rows
  * cut -f1 teosinte_genotypes.txt | wc = 975 so I got all of the teosinte rows
4. head -n 1 fang_et_al_genotypes.txt > header.txt
  * pulls header row out
5. cat header.txt maize_genotypes.txt > header_maize_genotypes.txt
  * adds header to maize genotypes.  
6.  cat header.txt teosinte_genotypes.txt > header_teosinte_genotypes.txt
  * adds header to teosinte file
7. awk -f transpose.awk header_maize_genotypes.txt > transposed_maize_genotypes.txt
  * tranposes maize_genotypes
8.  awk -f transpose.awk header_teosinte_genotypes.txt > transposed_teosinte_genotypes.txt
  * transposes teosinte_genotypes.txt
###Sort transposed files
1. sort -k1,1 snp_position.txt > sorted_snp_position.txt
  * this DOES NOT work.  it sorts the header row as well
2.  tail -n +2 snp_position.txt | sort -k1,1 > sorted_snp_position.txt
  * sorts but ignores first row.  resulting file no longer includes header information
3. head -n 1 snp_position.txt > snp_header.txt
4. tail -n +2 transposed_maize_genotypes.txt | sort -k1,1 > sorted_transposed_maize_genotypes.txt
5. tail -n +2 transposed_teosinte_genotypes.txt | sort -k1,1 > sorted_transposed_teosinte_genotypes.txt
###Join Sorted Files
1.  join -t $'\t' -1 1 -2 1 sorted_snp_position.txt sorted_transposed_maize_genotypes.txt > joined_maize_genotypes.txt
  * head -n 1 sorted_snp_position.txt | wc = 13 (you lose two columns because there is nothing in those columns except for the headers)
  * head -n 1 sorted_transposed_maize_genotypes.txt | wc = 1574
  * head -n 1 joined_maize_genotypes.txt | wc = 1586 which equals 1574+13-1 so checks out
2. join -t $'\t' -1 1 -2 1 sorted_snp_position.txt sorted_transposed_teosinte_genotypes.txt > joined_teosinte_genotypes.txt
  *  head -n 1 sorted_transposed_teosinte_genotypes.txt | wc = 976
  *  head -n 1 joined_teosinte_genotypes.txt | wc = 988 which equals 976+13-1

3. cut -f1,3,4,16-1586 joined_maize_genotypes.txt > pared_joined_maize_genotypes.txt
  * keeps only snp_id, chromosome, position, and genotype data
4. cut -f1,3,4,16-988 joined_teosinte_genotypes.txt > pared_joined_teosinte_genotypes.txt

###Generate a file for each Maize chromosome with SNP position increasing and missing data designated with "?"
1. awk '$2 == 1' pared_joined_maize_genotypes.txt | sort -k3,3n > chr1_increasing_SNPs_maize.txt
2. awk '$2 == 2' pared_joined_maize_genotypes.txt | sort -k3,3n > chr2_increasing_SNPs_maize.txt
3. awk '$2 == 3' pared_joined_maize_genotypes.txt | sort -k3,3n > chr3_increasing_SNPs_maize.txt
4. awk '$2 == 4' pared_joined_maize_genotypes.txt | sort -k3,3n > chr4_increasing_SNPs_maize.txt
5. awk '$2 == 5' pared_joined_maize_genotypes.txt | sort -k3,3n > chr5_increasing_SNPs_maize.txt
6. awk '$2 == 6' pared_joined_maize_genotypes.txt | sort -k3,3n > chr6_increasing_SNPs_maize.txt
7. awk '$2 == 7' pared_joined_maize_genotypes.txt | sort -k3,3n > chr7_increasing_SNPs_maize.txt
8. awk '$2 == 8' pared_joined_maize_genotypes.txt | sort -k3,3n > chr8_increasing_SNPs_maize.txt
9. awk '$2 == 9' pared_joined_maize_genotypes.txt | sort -k3,3n > chr9_increasing_SNPs_maize.txt
10. awk '$2 == 10' pared_joined_maize_genotypes.txt | sort -k3,3n > chr10_increasing_SNPs_maize.txt

But how do I use one command to make all 10 files at once?

###Generate a file for each Maize chromoseome with SNP position decreasing and missing data designated with "-"
1. sed -e 's/?/-/g' pared_joined_maize_genotypes.txt > maize_genotypes_unknowns_dashes.txt
2. awk '$2 == 1' maize_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr1_decreasing_SNPs_maize.txt
3. awk '$2 == 2' maize_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr2_decreasing_SNPs_maize.txt
4. awk '$2 == 3' maize_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr3_decreasing_SNPs_maize.txt
5. awk '$2 == 4' maize_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr4_decreasing_SNPs_maize.txt
6. awk '$2 == 5' maize_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr5_decreasing_SNPs_maize.txt
7. awk '$2 == 6' maize_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr6_decreasing_SNPs_maize.txt
8. awk '$2 == 7' maize_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr7_decreasing_SNPs_maize.txt
9. awk '$2 == 8' maize_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr8_decreasing_SNPs_maize.txt
10. awk '$2 == 9' maize_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr9_decreasing_SNPs_maize.txt
11. awk '$2 == 10' maize_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr10_decreasing_SNPs_maize.txt


###Generate a file for each Teosinte chromosome with SNP position increasing and missing data designated with "?"
1. awk '$2 == 1' pared_joined_teosinte_genotypes.txt | sort -k3,3n > chr1_increasing_SNPs_teosinte.txt
2. awk '$2 == 2' pared_joined_teosinte_genotypes.txt | sort -k3,3n > chr2_increasing_SNPs_teosinte.txt
3. awk '$2 == 3' pared_joined_teosinte_genotypes.txt | sort -k3,3n > chr3_increasing_SNPs_teosinte.txt
4. awk '$2 == 4' pared_joined_teosinte_genotypes.txt | sort -k3,3n > chr4_increasing_SNPs_teosinte.txt
5. awk '$2 == 5' pared_joined_teosinte_genotypes.txt | sort -k3,3n > chr5_increasing_SNPs_teosinte.txt
6. awk '$2 == 6' pared_joined_teosinte_genotypes.txt | sort -k3,3n > chr6_increasing_SNPs_teosinte.txt
7. awk '$2 == 7' pared_joined_teosinte_genotypes.txt | sort -k3,3n > chr7_increasing_SNPs_teosinte.txt
8. awk '$2 == 8' pared_joined_teosinte_genotypes.txt | sort -k3,3n > chr8_increasing_SNPs_teosinte.txt
9. awk '$2 == 9' pared_joined_teosinte_genotypes.txt | sort -k3,3n > chr9_increasing_SNPs_teosinte.txt
10. awk '$2 == 10' pared_joined_teosinte_genotypes.txt | sort -k3,3n > chr10_increasing_SNPs_teosinte.txt

But how do I use one command to make all 10 files at once???

###Generate a file for each Teosinte chromoseome with SNP position decreasing and missing data designated with "-"
1. sed -e 's/?/-/g' pared_joined_teosinte_genotypes.txt > teosinte_genotypes_unknowns_dashes.txt
2. awk '$2 == 1' teosinte_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr1_decreasing_SNPs_teosinte.txt
3. awk '$2 == 2' teosinte_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr2_decreasing_SNPs_teosinte.txt
4. awk '$2 == 3' teosinte_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr3_decreasing_SNPs_teosinte.txt
5. awk '$2 == 4' teosinte_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr4_decreasing_SNPs_teosinte.txt
6. awk '$2 == 5' teosinte_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr5_decreasing_SNPs_teosinte.txt
7. awk '$2 == 6' teosinte_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr6_decreasing_SNPs_teosinte.txt
8. awk '$2 == 7' teosinte_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr7_decreasing_SNPs_teosinte.txt
9. awk '$2 == 8' teosinte_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr8_decreasing_SNPs_teosinte.txt
10. awk '$2 == 9' teosinte_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr9_decreasing_SNPs_teosinte.txt
11. awk '$2 == 10' teosinte_genotypes_unknowns_dashes.txt | sort -k3,3nr > chr10_decreasing_SNPs_teosinte.txt

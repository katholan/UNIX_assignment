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

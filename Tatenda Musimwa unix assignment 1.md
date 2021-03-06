﻿﻿﻿﻿﻿﻿﻿﻿﻿﻿﻿#BCB546X
##Data processing

Data inspection

combined commands to form a loop

```
for i in snp position.txt fang_et_al_ genotypes.txt ; do echo "number of lines at: $(wc -l $i)"; echo  "numbers of columns : $(awk -F "\t" '{print NF; exit}' $i) $i"; echo "filesize: $(du -h $i)"; echo "idenity any ASCII and non-ASCII characters:" $(file $i); done
```

Outputs snp_position.txt

- number of lines at: 984 snp_position.txt

- numbers of columns : 15 snp_position.txt

- file size: 84K	snp_position.txt

- identity any ASCII and non-ASCII characters: snp_position.txt: ASCII text


fang_et_al_genotypes.txt

- number of lines at: 2783 fang _ et_ al_genotypes.txt
- numbers of columns : 986 fang _ et _ al _genotypes.txt
- filesize: 11M	fang _ et _ al_genotypes.txt
- identity any ASCII and non-ASCII characters: fang _ et _ al _ genotypes.txt: ASCII text, with very long lines


Data processing

Teosinte and maize file creation

create maize file by grepping out groups "ZMMIL,ZMMLR,ZMMMR


```
`grep -E "(ZMMIL|ZMMLR|ZMMMR)" fang_et_al_group.txt > maize_group.txt`
```

create teosinte file by grepping out groups "ZMPBA,ZMPIL,ZMPJA" without headers

```
`grep -E "(ZMPBA|ZMPIL|ZMPJA)" fang_et_al_group.txt > teosinte_group.txt`
```

extracted header from the fang_ et_ al_genotypes.txt


```
`head -n 1 fang_et_al_group.txt > header_fang_et_al_group.txt`

```

adding headers to files :- teosinte_ group.txt and maize_group.txt

```

`cat header_fang_et_al_group.txt maize_group.txt > headed_maize_group.txt`

`cat header_fang_et_al_group.txt teosinte_group.txt > headed_teosinte_group.txt`

```
removed 1,2 unnecessary columns using the cut command

```
`cut -f 3-968 headed_teosinte_group.txt > edit_headed_teosinte_group.txt`
cut -f 3-968 headed_maize_group.txt > edit_headed_maize_group.txt
```

Transpose the data files

Transposed files with headers

```
awk -f transpose.awk edit_headed_teosinte_group.txt > transposed_teosinte_group.txt
```
`awk -f transpose.awk edit_headed_maize_group.txt > transposed_maize_group.txt`
```


sorting all the files by SNP id

```
sort -k1,1 ../UNIX_Assignment/snp_position.txt > snp_position_sorted.txt
sort -k1,1 -c  snp_position_sorted.txt |echo $? # check the sorting and output was zero
sort -k1,1 transposed_teosinte_genotypes.txt > teosinte_sorted.txt # sort the transposed teosinte file 
sort -k1,1 -c  teosinte_sorted.txt |echo $? # check the sorting and output is zero
sort -k1,1 transposed_maize_genotypes.txt > maize_sorted.txt # sorting 
sort -k1,1 -c  maize_sorted.txt |echo $? # check sorting and output is zero implying sorted 
```
joining the file by the common column

```
join -1 1 -2 1 snp_position_sorted.txt maize_sorted.txt > maize_joined.txt # joining the file column one 

```
```
join -1 1 -2 1 snp_position_sorted.txt teosinte_sorted.txt > teosinte_joined.txt
```

checking the number of lines and compared with the joined files
 
`for i in *_sorted.txt; do echo "number of lines $i is $(wc -l $i)"; done` 

Output

```
`number of lines maize_sorted.txt is 966 maize_sorted.txt`

`number of lines snp_position_sorted.txt is 984 snp_position_sorted.txt`

`number of lines teosinte_sorted.txt is 966 teosinte_sorted.txt`

`number of lines maize_joined.txt is 965 maize_joined.txt`

`number of lines teosinte_joined.txt is 965 teosinte_joined.txt`
```
Sort by chromosome number

sorting by column 3 which has chromosome numbers

```
`for i in *_joined.txt; do sort -k3,3n *_joined.txt > ../Required_file/sortedbychr"$i" ; done`
```
Required file will have the final submission files

```
sort -k3,3n sortedbychr* | echo $? # check the sorting and all outputs were zero Implying well sorted 
```
Substitution with ? or - using sed command

```
`for i in sortedbychr* ; do sed 's/-t/?/g' $i > qmark_"$i"; done # replacing missing data with ?`

`for i in sortedbychr* ; do sed 's/?/-/g' $i > dash_"$i"; done # replacing missing data with dash (-)`
```
Extracting chromosomes to separate file

maize and teosinte with question marks 

```
`for i in {1..10}; do awk '$3=='$i'' qmark_sortedbychrmaize_joined.txt > maize_chr_"$i"_qmark.txt; done`

for i in {1..10}; do awk '$3=='$i'' qmark_sortedbychrteosinte_joined.txt > teosinte_chr_"$i"_qmark.txt; done
```
maize and teosinte with dash by chromosomes

```
`for i in {1..10}; do awk '$3=='$i'' dash_sortedbychrmaize_joined.txt > maize_chr_"$i"_dash.txt; done`
for i in {1..10}; do awk '$3=='$i'' dash_sortedbychrteosinte_joined.txt > teosinte_chr_"$i"_dash.txt; done
```
Sorting chromosomes based on increasing SNP position

Based on missing data with dash

```
for i in {1..10}; do sort -k4,4nr  maize_chr_"$i"_dash.txt > maize_chr"$i"_dash_decr_snp_position.txt; done
for i in {1..10}; do sort -k4,4nr  teosinte_chr_"$i"_dash.txt > teosinte_chr"$i"_dash_decr_snp_position.txt; done


```
Sorting chromosomes based on increasing SNP position

Based on missing data with qmark

```
for i in {1..10}; do sort -k4,4n maize_chr_"$i"_qmark.txt > maize_chr"$i"_qmark_incre_snp_position.txt; done
for i in {1..10}; do sort -k4,4n teosinte_chr_"$i"_qmark.txt > teosinte_chr"$i"_qmark_incre_snp_position.txt; done
```
**SNPs with unknown positions**


```
`grep "unknown" sortedbychrmaize_joined.txt > maize_unknown_position.txt`
`grep "unknown" sortedbychrteosinte_joined.txt > teosinte_unknown_position.txt`
```

**SNP's with multiple positions**

```
grep "multiple" sortedbychrteosinte_joined.txt > teosinte_multiple_position.txt
grep "multiple" sortedbychrmaize_joined.txt > maize_multiple_position.txt
```
my GitHub repository has three directories i.e. Original , working and Required files. 
- Dataset folder has the assignment datasets and assignment questions markdown
- Working folder has immediate files i.e. for data processing 
- Required folder has the final files required for the assignment



































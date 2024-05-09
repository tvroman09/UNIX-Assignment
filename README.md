
# UNIX Assignment

# Data Inspection


```python
# Look at first 10 lines of data in fang_et_al_genotypes.txt
$ head -n 10 fang_et_al_genotypes.txt
```


```python
#Get count of lines, words, characters
$ wc fang_et_al_genotypes.txt
```


```python
#get file size
$ du -h fang_et_al_genotypes.txt
```


```python
#print number of columns
$ tail -n +6 fang_et_al_genotypes.txt | awk -F "\t" '{print NF; exit}'
```

fang_et_al_genotypes.txt
- file size: 6.7M
- number of columns: 986
- number of lines: 2783
- number of words: 2744038
- number of characters: 11051939


```python
# Look at first 10 lines of data in fang_et_al_genotypes.txt
$ head -n 10 snp_position.txt
```


```python
#Get count of lines, words, characters
$ wc snp_position.txt
```


```python
#get file size
$ du -h snp_position.txt
```


```python
#print number of columns
$ tail -n +6 fang_et_al_genotypes.txt | awk -F "\t" '{print NF; exit}'
```

snp_position.txt
- file size: 49K
- number of columns: 986
- number of lines: 984
- number of words: 13198
- number of characters: 82763

# Data Processing


```python
#Looking at column 3 of fang data to look at all possible group names
$ grep -v "^#" fang_et_al_genotypes.txt | cut -f3 | sort | uniq -c | sort -n

# remove headers of fang data - will help for sorting, but saving to headers file for later use
$ head -n 1 fang_et_al_genotypes.txt > headers.txt

#selecting group names that represent maize and creating new file maize.txt
$ grep -E "(ZMMIL|ZMMMR|ZMMLR)" fang_et_al_genotypes.txt > maize.txt

#selecting group names that represent teosinte and creating new file teo.txt
$ grep -E "(ZMPBA|ZMPIL|ZMPJA)" fang_et_al_genotypes.txt > teo.txt

#combine file with headers with maize only data
$ cat headers.txt maize.txt > h_maize.txt

#combine file with headers with teosinte only data
$ cat headers.txt teo.txt > h_teo.txt

#extract columns 4-986 and save to new file for both maize and teo data
$ cut -f 4-986 h_maize.txt > maize_group.txt
$ cut -f 4-986 h_teo.txt > teo_group.txt

#select needed columns only from snp_position.txt
$ cut -f 1,3,4 snp_position.txt >snp_pos.txt

#transpose files using code given in project description
$ awk -f transpose.awk maize_group.txt > t_maize.txt
$ awk -f transpose.awk teo_group.txt > t_teo.txt

#before joining, must sort transposed data files based on first column of all
$ sort -k1,1 snp_pos.txt >sorted_snp.txt
$ sort -k1,1 t_maize.txt > sorted_m.txt
$ sort -k1,1 t_teo.txt > sorted_t.txt

#join sorted snp file with sorted teo/maize file based on the first columns of all files and save this to a new, joined file for each
$join -1 1 -2 1 -t $'\t' sorted_snp.txt sorted_t.txt > snp_tt.txt
$join -1 1 -2 1 -t $'\t' sorted_snp.txt sorted_m.txt > snp_m.txt

#combine sorted, joined data with original header file
$ cat headers.txt snp_m.txt > h_snp_m.txt
$ cat headers.txt snp_tt.txt > h_snp_t.txt

#make directories to create files in
$ mkdir DecMaize DecTeo IncMaize IncTeo

#extract data from final joined files and sort increaseing and decreasing based on chromosome number for both maize and teo (separately)
#increasing with "?" for missing data
$ for i in {1..10}; do awk '$2=='$i'' h_snp_m.txt | sort -k3,3n | sed 's/-/?/g'> IncMaize/incmaizechr"$i".txt; done
$ for i in {1..10}; do awk '$2=='$i'' h_snp_t.txt | sort -k3,3n | sed 's/-/?/g'> IncTeo/incteochr"$i".txt; done
#decreasing with "-" for missing data
$ for i in {1..10}; do awk '$2=='$i'' h_snp_m.txt | sort -k3,3n | sed 's/-/?/g'> DecMaize/decmaizechr"$i".txt; done
$ for i in {1..10}; do awk '$2=='$i'' h_snp_t.txt | sort -k3,3n | sed 's/-/?/g'> DecTeo/decteochr"$i".txt; done

#extract unknown and multiple data for maize and teo, do not need to be ordered
$ grep "unknown" h_snp_m.txt >maize_unknown.txt
$ grep "unknown" h_snp_t.txt >teo_unknown.txt
$ grep "multiple" h_snp_m.txt >maize_mul.txt
$ grep "multiple" h_snp_t.txt >teo_mul.txt



```


      Cell In[3], line 48
        $ for i in {1..10}; do awk '$2=='$i'' h_snp_m.txt | sort -k3,3n | sed 's/-/?/g'> IncMaize/incmaizechr"$i".txt; done
                                                                     ^
    SyntaxError: invalid decimal literal




```python

```

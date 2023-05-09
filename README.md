# UK Biobank non-migraine dataset 

The UK Biobank resource is extensive and the biomedical data recorded is very detailed. For complex diseases such as migraine, there is no straightforward answer like yes or no. Therefore, to have a non-migraine control cohort for future studies, we will have to go through different data fields, each with different data-coding, to hopefully remove as many samples with any migraine symptoms as possible.


### 01. ICD10 - WHO International Classification of Diseases
Firstly, data of health-related outcomes for each individual in the UK Biobank were recorded in several data fields using ICD10 codes. ICD10 is basically a code system for reporting human diseases and death. The ICD10 codes for migraine are as follows: 

| coding| meaning |
|-------|----------------|
| G430	| G43.0 Migraine without aura [common migraine] |
| G431	| G43.1 Migraine with aura [classical migraine] |
| G432	| G43.2 Status migrainosus |
| G433	| G43.3 Complicated migraine |
| G438	| G43.8 Other migraine |
| G439	| G43.9 Migraine, unspecified |

Also, the ICD10 codes for headache:

| coding| meaning |
|-------|----------------|
| G440	| G44.0 Cluster headache syndrome |
| G441	| G44.1 Vascular headache, not elsewhere classified |
| G442	| G44.2 Tension-type headache |
| G443	| G44.3 Chronic posttraumatic headache |
| G444	| G44.4 Drug-induced headache, not elsewhere classified |
| G448	| G44.8 Other specified headache syndromes |
| R51	  | R51 Headache

The UK Biobank data fields that use the ICD10 codes are as follows. You can click on the data-fields to learn more. 

| Data-Field | Description | 
| ---------- | ----------- |
| [41201](https://biobank.ndph.ox.ac.uk/ukb/field.cgi?id=41201)   | External causes - ICD10 (External causes of morbidity and mortality) | 
| [41202](https://biobank.ndph.ox.ac.uk/ukb/field.cgi?id=41202)     | Diagnoses - main ICD10 |
| [41204](https://biobank.ndph.ox.ac.uk/ukb/field.cgi?id=41204)      | Diagnoses - secondary main ICD10 |
| [41270](https://biobank.ndph.ox.ac.uk/ukb/field.cgi?id=41270)      | Diagnoses - ICD10 |

All the data-fields can be extracted from the UK Biobank data table located at `/mnt/work/datasets/compgen/ukbiobank/master/ukb43384.tab`. To know which columns corresponding to these data-fields, there is a meta-data file located in the same directory in html format. 

```
cut -f1,14873-14894,14895-14960,14989-15172,15744-15956 /mnt/work/datasets/compgen/ukbiobank/master/ukb43384.tab > s01.icd10.txt
```

After that, we can use this R code to remove those with migraine or headache. 

```R
codes <- c("G430", "G431", "G432", "G433", "G438", "G439", "G440", "G441", "G442", "G443", "G444", "G448","R51") ## ICD10 codes for migraine and headache

dat <- read.table("s01.icd10.txt", header=T, stringsAsFactors = FALSE) ## read data 
dat[is.na(dat)] <- "XXX" ##change all NA into somthing else
head(dat[,1:5])

df <- as.data.frame(matrix(nrow=nrow(dat)))
row.names(df) <- dat$f.eid	
for (i in 1:length(codes)){
	df[i] <- +( Reduce(`|`, lapply(dat, `==`, codes[i])))
	}
names(df) <- codes
head(df[1:5])

df$all <- ifelse(rowSums(df[1:length(codes)]) >0, 1, 0)
head(df[df$all==1,], 2)
sum(df$all)

colSums(df)

##  G430  G431  G432  G433  G438  G439  G440  G441  G442  G443  G444  G448   R51 all
##   48   306    11    27   108  3958   145    15   461    21    56    94 11169	15167

write.table(row.names(df[df$all==0,]), "s01.icd10.eid.txt", row.names=F, quote=F)
```

### 02. ICD9
For older hospital records, and also for some diagnosis, the ICD9 codes were used. 

| coding | meaning |
| ------ | ------- |
| 3460	| 3460 Classical migraine |
| 3461	| 3461 Common migraine |
| 3462	| 3462 Variants of migraine |
| 3468	| 3468 Other specified migraine |
| 3469	| 3469 Migraine, unspecified |
| 7840	| 7840 Headache

Similarly, the data-fields using the ICD9 are: 

41203	Categorical (multiple)	Diagnoses - main ICD9
	41205-0.0	8714	Categorical (multiple)	Diagnoses - secondary ICD9
  	41271-0.0	20302	Categorical (multiple)	Diagnoses - ICD9


cut -f1,14961-14988,15173-1520,15957-16003





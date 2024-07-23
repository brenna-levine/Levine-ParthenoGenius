# ParthenoGenius
A user-friendly heuristic for inferring presence and mechanism of facultative parthenogenesis from genetic and genomic data sets.

**Author:** Dr. Brenna A. Levine - levine.brenna.a@gmail.com

**How to cite:** Levine BA, Booth W. 2024. ParthenoGenius: A user-friendly heuristic for inferring presence and mechanism of facultative parthenogenesis from genetic and genomic data sets. https://github.com/brenna-levine/LEVINE_ParthenoGenius/tree/main

____________________________________________

# Installation

*ParthenoGenius* can be downloaded by using Git to clone the GitHub repository. It is ready to use once it has been downloaded:
```
git clone "https://github.com/brenna-levine/LEVINE_ParthenoGenius"
```
*ParthenoGenius* is writted in python and requires the pandas module (https://pandas.pydata.org).

____________________________________________

# Usage

The help menu can be accessed using the "-h" argument.

```
./ParthenoGenius.py -h
```
The mandatory arguments to run *ParthenoGenius* are an input file formatted as a .csv (e.g., mydata.csv) and a user-defined prefix for naming the output files (e.g., "mydata-out").

```
./ParthenoGenius.py mydata.csv mydata-out
```

The user may provide an optional estimated per-base error rate for Part 1 of the program (default value = 0.001) and an optional user-defined error rate for Part 2 of the program (no default).
```
./ParthenoGenius.py mydata.csv mydata-out --error 0.01 --P2_user_defined_error 0.01
```
_________________________________________________

# Input File

The input file is a .csv file containing diploid co-dominant locus names and the genotypes for a mother and offspring. Missing data can be coded as -9. The file format is similar to that of a Structure file, with two lines for each individual. To create the input file for *ParthenoGenius* from a Structure file, the user can delete the columns that do not contain the sample IDs or genotypes. If the user does not have a Structure file but rather a VCF file or other common bioinformatics file format, the user can use PGDSpider (https://cmpg.unibe.ch/software/PGDSpider/", software with a graphical user interface, to convert their file to a Structure file. The user should then save their file as a csv. Alleles can be written as letters or numbers, with no restrictions on the length of the allele (i.e., SNP allele vs microsatellite length). For example:

```
,SNP1,SNP2,SNP3,SNP4,SNP5
MOM_1,1,1,1,-9,0
MOM_2,1,0,1,1,0
OFF_1,1,0,0,0,0
OFF_2,1,0,1,0,-9
```
Five test data sets are provided in the repository in the ```TEST-DATA-SETS``` directory to further illustrate the necessary input file format. The input file can be easily generated generated by modifying a Structure file, a common file format generated by bioinformatics software. To modify the structure file, delete all columns other than the genotype columns and the sample ID columns, and save the file as a CSV. The input file should only contain data for the mother and one offspring. If numerous offspring are to be considered, the file preparation script ```PG_file_prep.sh``` should be executed to generate individual files, and the wrapper script ```PG_wrapper.sh``` should be used to iterate through the files.

If you have a large genetic parentage data set containing many parents and offspring, you can use the file preparation script (```PG_file_prep.sh```) to generate the requisite input files for *ParthenoGenius*. To do so, you will need two files named ```ID_pairs.txt``` and ```genotypes.csv```.
The file ```ID_pairs.txt``` should contain the headers ```OFFSPRING_ID``` AND ```MOM_ID```, followed by the ID's of respective offspring and mothers. Data should be tab-delimited. Examples of these files are in the ```TUTORIAL``` directory. For example:
```
OFFSPRING_ID  MOM_ID
NVP-003  NVP-004
NVP-006  NVP-004
NVP-009  NVP-004
```
The ```genotypes.csv``` file should be a file formatted similarly to the Structure-like input file above, but may contain many parents, offspring, and unrelated individuals. Again, if you have a population-wide Structure file, you can easily modify it to generate the input file for this script by deleting all columns except for the sample ID and genotypes and saving the file as a CSV. For example:
```
,SNP_1,SNP_2,SNP_3,SNP_4, SNP_5
NVP-003,1,1,0,0,1
NVP-003,1,1,1,1,1
NVP-004,1,1,0,0,0
NVP-004,1,1,1,1,0
NVP-006,0,1,1,1,1
NVP-006,1,1,1,0,1
NVP-009,0,1,-9,0,0
NVP-009,0,0,0,1,0
NVP-010,0,-9,1,1,0
NVP-010,1,-9,1,1,0
```
Note - the IDs in the ```ID_pairs.txt``` and ```genotypes.csv``` must be the same. These files must be in the same directory as the ```PG_file_prep.sh``` script.

Once you have the two files prepared, ```PG_file_prep.sh``` can be run from the command-line using the following command:

```./PG_file_prep.sh```


```PG_file_prep.sh``` will generate *ParthenoGenius* input files for each mother/offspring pair. One can then run all generated input files through *ParthenoGenius* using ```PG_wrapper.sh```.
The only contents of the directory containing the input files should be the input files. To run the wrapper script, provide the input file directory (<input_directory>), the output file directory (<output_directory), the error rate for Part 1 (Part_1_error_rate), and optionally a user-defined error rate for Part 2 (P2_user_defined_error_rate>) as command line arguments. Note - the output directory does not have to already exist and is optionally created by the program if it does not yet exist. Execute the wrapper script from the command line as follows:

```./PG_wrapper.sh <input_directory> <output_directory> <Part_1_error_rate> <P2_user_defined_error_rate>```

Output files will be automatically named according to the input file name and can be found in the output directory provided as an argument by the user. 

# Tutorial

The following tutorial demonstrates how to use ```PG_file_prep.sh``` and ```PG_wrapper.sh``` to prepare input files from a larger .csv file and iteratively run ```ParthenoGenius.py``` on all of the generated input files. For the tutorial, we are using the same data as is present in the ```NEPALESE-VIPER-PARTH-TEST``` directory. This directory contains two emtpy directories (```INFILES``` AND ```OUTFILES```), the ```PG_file_prep.sh``` script, a larger .csv containing genotypes of multiple offspring and their mothers (```genotypes.csv```) and a file that specifies mother/offspring pairs (```ID_pairs.txt```).

First, navigate to the tutorial directory from the ParthenoGenius home directory.

```cd TUTORIAL```

Next, use ```less``` to view the ```ID_pairs.txt``` and ```genotypes.csv``` file. Notice that the ID names in the ```ID_pairs.txt``` file match those in the ```genotypes.csv``` file. Now, execute the local copy of ```PG_file_prep.sh```. Note - this script must be in the same directory as the files in order to run.

```./PG_file_prep.sh```

View the contents of the directory with ```ls```. You should now see three infiles corresponsing to the three sets of mother/offspring pairs in the ```ID_pairs.txt``` file. Notice that the files names correspond to the IDs of the offspring in each mother/offspring pair.

Now, move all of the newly generated infiles to the ```INFILES/``` directory.

```mv NVP* INFILES/```

Then, navigate back to main ParthenoGenius directory.

```../```

To run *ParthenoGenius* on all input files in this new directory and store the results in the outfile directory, type the following on the command line:

```./PG_wrapper-TUTORIAL.sh TUTORIAL/INFILES TUTORIAL/OUTFILES 0.01 0.01```

This command declares the path to the input directory (TUTORIAL/INFILES), the path to the output directory (TUTORIAL/OUTFILES), the user-defined error rate for Part 1 (0.01; default = 0.001), and the optional user-defined error rate for Part 2 (0.01).

Upon completion of the wrapper script, navigate to the tutorial outfile directory.

```cd TUTORIAL/OUTFILES```

Use ```ls``` to view the contents of the directory. Notice that there are now three sets of outfiles - one set for each mother/offspring pair. 
`







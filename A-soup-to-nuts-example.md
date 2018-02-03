In the normal workflow of supervised machine learning one creates a training set, trains an algorithm using that set, validates the accuracy of the trained algorithm, and then finally applies the trained algo to real data. We want to take you the user through that full process using `diploSHIC.py` here. 

We have created a rather large directory that contains everything you need to replicate the mosquito genome results shown in Kern and Schrider (2018). The directory contains raw simulation output from `discoal` for hard and soft sweeps and regions unlinked to sweeps which condition upon the demographic model for the Burkino Faso sample (BFS) from our recent AG1000G consortium paper. It also contains a large VCF file of genotype data from that paper, a masking file which shows which regions of the genome were sequenced, and a simple text file that maps the individual sample IDs to populations to fish the relevant genotypes out of the VCF file. 

Start by downloading the example directory like so
```
$ wget http://kerndev.rutgers.edu/~adk/diploSHIC/exampleApplication.tar.gz
```

## diploSHIC usage
In the normal workflow of supervised machine learning one creates a training set, trains an algorithm using that set, validates the accuracy of the trained algorithm, and then finally applies the trained algo to real data. We want to take you the user through that full process using `diploSHIC.py` here. 

We have created a rather large directory that contains everything you need to replicate the mosquito genome results shown in Kern and Schrider (2018). The directory contains raw simulation output from `discoal` for hard and soft sweeps and regions unlinked to sweeps which condition upon the demographic model for the Burkino Faso sample (BFS) from our recent AG1000G consortium paper. It also contains a large VCF file of genotype data from that paper, a masking file which shows which regions of the genome were sequenced, and a simple text file that maps the individual sample IDs to populations to fish the relevant genotypes out of the VCF file. 

Start by downloading and unpacking the example directory. Throughout this example I'm assuming that you are in the diploSHIC directory that you cloned from github
```
$ wget http://kerndev.rutgers.edu/~adk/diploSHIC/exampleApplication.tar.gz && tar zxvf exampleApplication.tar.gz
```
The relevant files are as follows
* ag1000g.phase1.ar3.pass.biallelic.3R.vcf.28000000-29000000.gz -- VCF genotype file for a region of Chr3R from AG1000G phase1
* Anopheles-gambiae-PEST_CHROMOSOMES_AgamP3.accessible.fa.gz -- the masking file
* samples_pops.txt -- file that maps individual samples to populations
* hard_*.msOut.gz -- simulation for hard sweeps. the number refers to the subwindow where the sweep ocurred
* soft_*.msOut.gz -- simulation for soft sweeps. the number is as above
* neut.msOut.gz -- simulations for regions unlinked to sweeps

### A brief note on simulations
The way we have set up S/HIC (Schrider and Kern 2016) and diploS/HIC (Kern and Schrider 2018) is that it takes five classes of data as its input (hard sweeps, soft sweeps, regions linked to hard sweeps, regions linked to soft sweeps, and regions unlinked to sweeps (neutral loci). We get the first four classes of data by performing coalescent simulations of a large region of the chromosome with a single hard or soft sweep that has occurred at a different position along the genome. We create these by assuming that the sweep has occurred at the center of one of our 11 subwindows. Those that occur in the 5th subwindow, i.e. the center of the larger simulated region, are treated as our hard or soft sweep cases. Those that occur in windows 0-4 or 6-11 are considered the linked classes. 

The `discoal` command lines used for each simulation are given in the first line of the `*.msOut.gz` files. Let's look quickly at `hard_5.msOut.gz`
```
$ zcat /scratch/ak917/exampleApplication/hard_5.msOut.gz | head -n 1
discoal 162 2000 55000 -Pt 1750.204699 17502.046985 -Pre 19252.251684 57756.755051 \
-en 0.000038 0 1.001158 -en 0.000077 0 1.004693 -en 0.000117 0 1.017850 -en 0.000157 0 1.019335 \ 
-en 0.000198 0 1.022229 -en 0.000240 0 1.025899 -en 0.000282 0 1.030126 -en 0.000326 0 1.030824 \ 
-en 0.000369 0 1.031031 -en 0.000459 0 1.031171 -en 0.000891 0 1.030126 -en 0.000943 0 1.027106 \ 
-en 0.000995 0 1.019246 -en 0.001047 0 1.019335 -en 0.001100 0 1.019086 -en 0.001154 0 1.019246 \ 
-en 0.001209 0 1.020601 -en 0.001265 0 1.018926 -en 0.001321 0 1.013910 -en 0.001378 0 1.004683 \ 
-en 0.001435 0 1.000118 -en 0.001493 0 0.985278 -en 0.001551 0 0.967523 -en 0.001608 0 0.621680 \ 
-en 0.001646 0 0.599478 -en 0.001683 0 0.583576 -en 0.001719 0 0.576138 -en 0.001756 0 0.564006 \ 
-en 0.001792 0 0.559137 -en 0.001829 0 0.555749 -en 0.001866 0 0.552085 -en 0.001904 0 0.551380 \ 
-en 0.001942 0 0.550215 -en 0.002059 0 0.550213 -en 0.002226 0 0.550215 -en 0.002751 0 0.550213 \ 
-en 0.004234 0 0.550215 -en 0.004410 0 0.549391 -en 0.004502 0 0.549190 -en 0.004596 0 0.523247 \ 
-en 0.004688 0 0.219782 -en 0.004727 0 0.218488 -en 0.005243 0 0.218480 -en 0.005416 0 0.217284 \
-en 0.005477 0 0.217170 -en 0.005607 0 0.217036 -en 0.005675 0 0.216631 -en 0.005818 0 0.216410 \ 
-en 0.006054 0 0.215218 -en 0.006138 0 0.215008 -en 0.006226 0 0.214357 -en 0.006317 0 0.214284 \ 
-en 0.006412 0 0.213176 -en 0.006510 0 0.213172 -en 0.006613 0 0.212340 -en 0.006721 0 0.211736 \ 
-en 0.006833 0 0.211688 -en 0.006950 0 0.211334 -en 0.007630 0 0.127176 -en 0.007725 0 0.124824 \ 
-en 0.008040 0 0.128665 -en 0.008162 0 0.133725 -en 0.008297 0 0.137004 -en 0.008444 0 0.140183 \ 
-en 0.008606 0 0.143539 -en 0.008782 0 0.147709 -en 0.008978 0 0.155927 -en 0.009200 0 0.160223 \ 
-en 0.009446 0 0.165811 -en 0.009723 0 0.168947 -en 0.010029 0 0.177921 -en 0.010380 0 0.211421 \ 
-en 0.010838 0 0.230486 -en 0.011387 0 0.238488 -en 0.012014 0 0.215713 -en 0.012645 0 0.155886 \
-en 0.013155 0 0.078653 -en 0.013444 0 0.042227 -en 0.013620 0 0.018138 -en 0.013706 0 0.015204 \
-en 0.013790 0 0.014786 -en 0.013884 0 0.014356 -en 0.014124 0 0.017850 -en 0.014322 0 0.039856 \
-en 0.014876 0 0.100438 -en 0.016669 0 0.262716 -en 0.022924 0 0.049834 -en 0.024585 0 0.009407 \
-en 0.025056 0 0.001776 -en 0.025204 0 0.007266 -en 0.026415 0 0.038498 \
-ws 0 -Pa 2500.292426 250029.242646 -Pu 0.000000 0.000040 -x 0.5
```
This is a bit ugly because of all the `-en` flags which specify the demographic size change history of the BFS sample, so I have added backslashes and line breaks to make it appear nicer on this wiki. Ignore those for a minute and instead focus on the options before and after the demography. In particular you can see that `-x 0.5` has been given indicating that the hard sweep is at the middle of our simulated region. If instead we look in `hard_0.msOut.gz` there we set `-x 0.045454545454545456` indicating that the sweep occurred at the center of the leftmost subwindow.

Hopefully this simulation output will give you a very good headstart on how to create your own simulations with `discoal` to generate your own training set for `diploSHIC.py`. 

### Calculating feature vectors from simulations
Once you have simulation output, we need to compute summary statistics and feature vectors on those. To do that we will use `diploSHIC.py` in its fvecSim mode. These simulations are large so the computation will take a while. I usually do this on a cluster but to make things simple I'll give an example of launching all the jobs on a multicore machine
```
$ for f in exampleApplication/*.msOut.gz; do python diploSHIC.py fvecSim diploid $f $f.diploid.fvec --totalPhysLen 55000 --maskFileName exampleApplication/Anopheles-gambiae-PEST_CHROMOSOMES_AgamP3.accessible.fa.gz --chrArmsForMasking 3R & done
```
this will launch all the jobs to the background. Go get lunch-- this will take a couple of hours to complete. 

### Make a balanced training set 
Once this is complete we will create a balanced training set (i.e. the same number of examples per class) using the helper script `makeTrainingSets.py`.
```
$ mkdir rawFVFiles && mv /scratch/ak917/exampleApplication/*.fvec rawFVFiles/
$ mkdir trainingSets
$ python makeTrainingSets.py rawFVFiles/neut.msOut.gz.diploid.fvec rawFVFiles/soft \
rawFVFiles/hard 5 0,1,2,3,4,6,7,8,9,10 trainingSets/
```
et voila! our training sets are ready.

### train diploS/HIC
We will now move on to training a classifier. 

### feature vectors for example mosquito data

```
$ python diploSHIC.py fvecVcf diploid \
exampleApplication/ag1000g.phase1.ar3.pass.biallelic.3R.vcf.28000000-29000000.gz 3R 53200684 \ exampleApplication/ag1000g.phase1.ar3.pass.biallelic.3R.vcf.28000000-29000000.gz.diploid.fvec \
--targetPop BFS --sampleToPopFileName exampleApplication/samples_pops.txt --winSize 55000 \ 
--maskFileName exampleApplication/Anopheles-gambiae-PEST_CHROMOSOMES_AgamP3.accessible.fa.gz
```
again that will take a while to run. 

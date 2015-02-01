Short read quality and trimming
===============================

Log into the HPC with SSH; use your MSU NetID and log into the machine
'gateway.hpcc.msu.edu'.  There copy/paste::

   module load powertools
   getexample RNAseq-model

and you should see something about linking.

0. Getting the data
-------------------

We'll be using a few RNAseq data sets from Fagerberg et al., `Analysis
of the Human Tissue-specific Expression by Genome-wide Integration of
Transcriptomics and Antibody-based Proteomics
<http://www.mcponline.org/content/13/2/397.full>`__.

You can get this data from the European Nucleotide Archive under
ERP003613 -- go to http://www.ebi.ac.uk/ena/data/view/ERP003613
to see all the samples.

I picked two samples: `salivary gland
<http://www.ebi.ac.uk/ena/data/view/SAMEA2151887>`__ and `lung
<http://www.ebi.ac.uk/ena/data/view/SAMEA2155770>`__.  Note that each
sample has two replicates, and each replicate has two files.

**Don't download them**, but if you were downloading these yourself,
you would want the "Fastq files (ftp)", both File 1 and File 2.  (They
take a few hours to download!)

We've already loaded the data onto the MSU HPC, and you've loaded
it with 'module load powertools'.

To log into a compute node, type::

   ~/RNAseq-model/login.sh

If this doesn't work, do::

   ssh dev-intel14-phi

Now do::

   ls -la ~/RNAseq-model/data/

You'll see something like ::

   -r--r--r-- 1 mscholz common-data 3714262571 Dec  4 08:44 ERR315325_1.fastq
   -r--r--r-- 1 mscholz common-data 3714262571 Dec  4 08:44 ERR315325_2.fastq
   -r--r--r-- 1 mscholz common-data 2365633645 Dec  4 08:44 ERR315326_1.fastq
   -r--r--r-- 1 mscholz common-data 2365633645 Dec  4 08:44 ERR315326_2.fastq

which tells you that this file is 900,000,000 bytes or about 900 MB.
Quite large!

1. Copying in some data to work with.
-------------------------------------

First, make a directory::

   mkdir ~/rnaseq
   cd ~/rnaseq

Copy in a subset of the data (100,000 reads)::

   head -400000 ~/RNAseq-model/data/ERR315325_1.fastq | gzip > salivary_repl1_R1.fq.gz
   head -400000 ~/RNAseq-model/data/ERR315325_2.fastq | gzip > salivary_repl1_R2.fq.gz

These are FASTQ files -- let's take a look::

   less salivary_repl1_R1.fq.gz

(type 'q' to exit less)

Question:

* why are some files named ERR*?
* why are some files named salivary*?
* why is there R1 and R2 in the name?

Links:

* `FASTQ Format <http://en.wikipedia.org/wiki/FASTQ_format>`__

2. FastQC
---------

We're going to use `FastQC <http://www.bioinformatics.babraham.ac.uk/projects/fastqc/>`__ to summarize the data.

First, we need to load the FastQC software into our account::

   module load FastQC/0.11.2

(You have to do this each time you log in and want to use FastQC.)

Now, run FastQC on both of the salivary gland files::

   fastqc salivary_repl1_R1.fq.gz
   fastqc salivary_repl1_R2.fq.gz

Now type 'ls'::

   ls

and you will see ::

   salivary_repl1_R1_fastqc.html
   salivary_repl1_R1_fastqc.zip
   salivary_repl1_R2_fastqc.html
   salivary_repl1_R2_fastqc.zip

Copy these to your laptop and open them in a browser.  If you're on a
Mac or Linux machine, you can type::

   scp username@hpc.msu.edu:salivary*fastqc.* /tmp

and then open the html files in your browser.  For Windows, we'll
figure it out ;).

You can also view my versions: `salivary_repl1_R1_fastqc.html
<http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/salivary_repl1_R1_fastqc.html>`__
and `salivary_repl1_R2_fastqc.html
<http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/salivary_repl1_R2_fastqc.html>`__

Questions:

* What should you pay attention to in the FastQC report?
* Which is "better", R1 or R2?

Links:

* `FastQC <http://www.bioinformatics.babraham.ac.uk/projects/fastqc/>`__
* `FastQC tutorial video <http://www.youtube.com/watch?v=bz93ReOv87Y>`__

3. Trimmomatic
--------------

Now we're going to do some trimming!  We'll be using
`Trimmomatic <http://www.usadellab.org/cms/?page=trimmomatic>`__.
For
a discussion of optimal RNAseq trimming strategies, see `MacManes, 2014 <http://journal.frontiersin.org/Journal/10.3389/fgene.2014.00013/abstract>`__.

First, load the Trimmomatic software::

   module load Trimmomatic/0.32

Next, run Trimmomatic::

   java -jar $TRIM/trimmomatic PE salivary_repl1_R1.fq.gz salivary_repl1_R2.fq.gz\
        salivary_repl1_R1.qc.fq.gz s1_se salivary_repl1_R2.qc.fq.gz s2_se \
        ILLUMINACLIP:$TRIM/adapters/TruSeq3-PE.fa:2:40:15 \
        LEADING:2 TRAILING:2 \                            
        SLIDINGWINDOW:4:2 \
        MINLEN:25

You should see output that looks like this::

   ...
   Input Read Pairs: 100000 Both Surviving: 95236 (95.24%) Forward Only Surviving: 4764 (4.76%) Reverse Only Surviving: 0 (0.00%) Dropped: 0 (0.00%)
   TrimmomaticPE: Completed successfully

Questions:

* How do you figure out what the parameters mean?
* How do you figure out what parameters to use?
* What adapters do you use?
* What version of Trimmomatic are we using here? (And FastQC?)
* Are parameters different for RNAseq and genomic?
* What's with these annoyingly long and complicated filenames?
* What do we do with the single-ended files (s1_se and s2_se?)

Links:

* `Trimmomatic <http://www.usadellab.org/cms/?page=trimmomatic>`__

4. FastQC again
---------------

Run FastQC again::

   fastqc salivary_repl1_R1.qc.fq.gz
   fastqc salivary_repl1_R2.qc.fq.gz

(Note that you don't need to load the module again.)

Copy them to your laptop and open them, OR you can view mine: `salivary_repl1_R1.qc_fastqc.html
<http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/salivary_repl1_R1.qc_fastqc.html>`__
and `salivary_repl1_R2.qc_fastqc.html
<http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/salivary_repl1_R2.qc_fastqc.html>`__

Let's take a look at the output files::

   less salivary_repl1_R1.qc.fq.gz

(again, use 'q' to exit less).

Questions:

* Why are some of the reads shorter than others?
* is the quality trimmed data "better" than before?
* Does it matter that you still have adapters!?

Next: :doc:`m-tophat`

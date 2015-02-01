Short read quality and trimming
===============================

.. note::

   Reminder: if you're on Windows, you should install `mobaxterm <http://mobaxterm.mobatek.net/download.html>`__.

Log into the HPC with SSH; use your MSU NetID and log into the machine
'hpc.msu.edu'.  There copy/paste::

   cd
   module load powertools
   getexample RNAseq-semimodel

This will put all of the example files for today in your home directory
under the directory 'RNAseq-semimodel'.

0. Getting the data
-------------------

http://genomebiology.com/content/14/3/R26

http://www.ebi.ac.uk/ena/data/view/SRA055442

http://www.ebi.ac.uk/ena/data/view/SAMN01096082
http://www.ebi.ac.uk/ena/data/view/SAMN01096083

http://www.ebi.ac.uk/ena/data/view/SAMN01096084
http://www.ebi.ac.uk/ena/data/view/SAMN01096085

Note that each
sample has two replicates, and each replicate has two files.

**Don't download them**, but if you were downloading these yourself,
you would want the "Fastq files (ftp)", both File 1 and File 2.  (They
take a few hours to download!)

We've already loaded the data onto the MSU HPC, and you've loaded
it with 'module load powertools'.

To log into a compute node, type::

   ~/RNAseq-semimodel/login.sh

If this doesn't work, do::

   ssh dev-intel14-phi

Now do::

   ls -l ~/RNAseq-semimodel/data/

You'll see something like ::

    -r--r--r-- 1 mscholz common-data 6781517200 Dec  9 09:46 SRR534005_1.fastq.gz
    -r--r--r-- 1 mscholz common-data 7023515467 Dec  9 09:50 SRR534005_2.fastq.gz
    -r--r--r-- 1 mscholz common-data 7285848617 Dec  9 09:41 SRR534006_1.fastq.gz
    -r--r--r-- 1 mscholz common-data 7542383700 Dec  9 09:43 SRR534006_2.fastq.gz
    -r--r--r-- 1 mscholz common-data 7219923066 Dec  9 09:47 SRR536786_1.fastq.gz
    -r--r--r-- 1 mscholz common-data 7467116873 Dec  9 09:49 SRR536786_2.fastq.gz
    -r--r--r-- 1 mscholz common-data 7694614208 Dec  9 09:40 SRR536787_1.fastq.gz
    -r--r--r-- 1 mscholz common-data 7944043814 Dec  9 09:44 SRR536787_2.fastq.gz

These files are each approximately 7-8 GB in size!

1. Copying in some data to work with.
-------------------------------------

First, make a directory::

   mkdir ~/rnaseq
   cd ~/rnaseq

Copy in a subset of the data (100,000 reads)::

   gunzip -c ~/RNAseq-semimodel/data/SRR534005_1.fastq.gz | head -400000 | gzip > female_repl1_R1.fq.gz 
   gunzip -c ~/RNAseq-semimodel/data/SRR534005_2.fastq.gz | head -400000 | gzip > female_repl1_R2.fq.gz 

These are FASTQ files -- let's take a look::

   less female_repl1_R1.fq.gz

(type 'q' to exit less)

Question:

* why are some files named SRR*?
* why are some files named female*?
* why are there R1 and R2 in the name?

Links:

* `FASTQ Format <http://en.wikipedia.org/wiki/FASTQ_format>`__

2. FastQC
---------

We're going to use `FastQC <http://www.bioinformatics.babraham.ac.uk/projects/fastqc/>`__ to summarize the data.

First, we need to load the FastQC software into our account::

   module load FastQC/0.11.2

(You have to do this each time you log in and want to use FastQC.)

Now, run FastQC on both of the female files::

   fastqc female_repl1_R1.fq.gz
   fastqc female_repl1_R2.fq.gz

Now type 'ls'::

   ls

and you will see ::

   female_repl1_R1_fastqc.html
   female_repl1_R1_fastqc.zip
   female_repl1_R2_fastqc.html
   female_repl1_R2_fastqc.zip

Copy these to your laptop and open them in a browser.  If you're on a
Mac or Linux machine, you can type::

   scp username@hpc.msu.edu:rnaseq/female*fastqc.* /tmp

and then open the html files in your browser.  For Windows, if you're using
mobaxterm, most of you should have a file transfer window on the left.
Click 'refresh' (green circle icon fourth from the left) and then navigate
into the 'rnaseq' folder; you should see the 'female_repl...' files there.
Drag and drop those onto your Windows machine.

You can also view my versions: `female_repl1_R1_fastqc.html
<http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/female_repl1_R1_fastqc.html>`__
and `female_repl1_R2_fastqc.html
<http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/female_repl1_R2_fastqc.html>`__

Questions:

* What should you pay attention to in the FastQC report?
* Which is "better", R1 or R2?

Links:

* `FastQC <http://www.bioinformatics.babraham.ac.uk/projects/fastqc/>`__
* `FastQC tutorial video <http://www.youtube.com/watch?v=bz93ReOv87Y>`__

3. Trimmomatic
--------------

Now we're going to do some trimming!  We'll be using
`Trimmomatic <http://www.usadellab.org/cms/?page=trimmomatic>`__.  For
a discussion of optimal RNAseq trimming strategies, see `MacManes, 2014 <http://journal.frontiersin.org/Journal/10.3389/fgene.2014.00013/abstract>`__.

First, load the Trimmomatic software::

   module load Trimmomatic/0.32

Next, run Trimmomatic::

   java -jar $TRIM/trimmomatic PE female_repl1_R1.fq.gz female_repl1_R2.fq.gz\
        female_repl1_R1.qc.fq.gz s1_se female_repl1_R2.qc.fq.gz s2_se \
        ILLUMINACLIP:$TRIM/adapters/TruSeq3-PE.fa:2:40:15 \
        LEADING:2 TRAILING:2 \                            
        SLIDINGWINDOW:4:2 \
        MINLEN:25

You should see output that looks like this::

   ...
   Quality encoding detected as phred33
   Input Read Pairs: 100000 Both Surviving: 95583 (95.58%) Forward Only Surviving: 4262 (4.26%) Reverse Only Surviving: 86 (0.09%) Dropped: 69 (0.07%)
   ...

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

   fastqc female_repl1_R1.qc.fq.gz
   fastqc female_repl1_R2.qc.fq.gz

(Note that you don't need to load the module again.)

Copy them to your laptop and open them, OR you can view mine: `female_repl1_R1.qc_fastqc.html
<http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/female_repl1_R1.qc_fastqc.html>`__
and `female_repl1_R2.qc_fastqc.html
<http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/female_repl1_R2.qc_fastqc.html>`__

Let's take a look at the output files::

   less female_repl1_R1.qc.fq.gz

(again, use 'q' to exit less).

Questions:

* Why are some of the reads shorter than others?
* is the quality trimmed data "better" than before?
* Does it matter that you still have adapters!?

5. Subset and trim the rest of the sequences
--------------------------------------------

Copy and paste all of the below at once::

   gunzip -c ~/RNAseq-semimodel/data/SRR534006_1.fastq.gz | head -400000 | gzip > female_repl2_R1.fq.gz 
   gunzip -c ~/RNAseq-semimodel/data/SRR534006_2.fastq.gz | head -400000 | gzip > female_repl2_R2.fq.gz 

   gunzip -c ~/RNAseq-semimodel/data/SRR536786_1.fastq.gz | head -400000 | gzip > male_repl1_R1.fq.gz 
   gunzip -c ~/RNAseq-semimodel/data/SRR536786_2.fastq.gz | head -400000 | gzip > male_repl1_R2.fq.gz 

   gunzip -c ~/RNAseq-semimodel/data/SRR536787_1.fastq.gz | head -400000 | gzip > male_repl2_R1.fq.gz 
   gunzip -c ~/RNAseq-semimodel/data/SRR536787_2.fastq.gz | head -400000 | gzip > male_repl2_R2.fq.gz 

   java -jar $TRIM/trimmomatic PE female_repl2_R1.fq.gz female_repl2_R2.fq.gz\
        female_repl2_R1.qc.fq.gz s1_se female_repl2_R2.qc.fq.gz s2_se \
        ILLUMINACLIP:$TRIM/adapters/TruSeq3-PE.fa:2:40:15 \
        LEADING:2 TRAILING:2 \                            
        SLIDINGWINDOW:4:2 \
        MINLEN:25

   java -jar $TRIM/trimmomatic PE male_repl1_R1.fq.gz male_repl1_R2.fq.gz\
        male_repl1_R1.qc.fq.gz s1_se male_repl1_R2.qc.fq.gz s2_se \
        ILLUMINACLIP:$TRIM/adapters/TruSeq3-PE.fa:2:40:15 \
        LEADING:2 TRAILING:2 \                            
        SLIDINGWINDOW:4:2 \
        MINLEN:25
   
   java -jar $TRIM/trimmomatic PE male_repl2_R1.fq.gz male_repl2_R2.fq.gz\
        male_repl2_R1.qc.fq.gz s1_se male_repl2_R2.qc.fq.gz s2_se \
        ILLUMINACLIP:$TRIM/adapters/TruSeq3-PE.fa:2:40:15 \
        LEADING:2 TRAILING:2 \                            
        SLIDINGWINDOW:4:2 \
        MINLEN:25
   

Next: :doc:`s-building-a-reference`

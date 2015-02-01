Building a new reference transcriptome
======================================

The distinguishing feature of (what I call) semi-model organisms is that
while they may have a decent genome reference, their transcriptome
annotation is poor.  There can be several reasons for this, but generally
it boils down to lack of resources and/or attention -- it takes a *lot*
of effort to build a high quality transcriptome!

For this purpose, we've already installed the chicken reference genome
set on the HPC (as part of the data you loaded at the beginning).  In
this case we've loaded in the `Illumina iGenomes project
<http://cufflinks.cbcb.umd.edu/igenomes.html>`__ into the RNAseq-semimodel
location.

See paper:

    http://www.ncbi.nlm.nih.gov/pmc/articles/PMC3334321/

Map all the reads to the genome with TopHat
-------------------------------------------

.. @@ add links etc.

We'll be using the `TopHat software
<http://ccb.jhu.edu/software/tophat/manual.shtml>`__.

Do::

   module load TopHat2/2.0.12

   tophat -p 4 \
       -G  $HOME/RNAseq-semimodel/reference/Gallus_gallus/UCSC/galGal3/Annotation/Genes/genes.gtf \
       --transcriptome-index=$HOME/RNAseq-semimodel/tophat/transcriptome \
       -o tophat_all \
       $HOME/RNAseq-semimodel/reference/Gallus_gallus/UCSC/galGal3/Sequence/Bowtie2Index/genome \
    female_repl1_R1.qc.fq.gz,male_repl1_R1.qc.fq.gz,female_repl2_R1.qc.fq.gz,male_repl2_R1.qc.fq.gz \
    female_repl1_R2.qc.fq.gz,male_repl1_R2.qc.fq.gz,female_repl2_R2.qc.fq.gz,male_repl2_R2.qc.fq.gz

Questions:

* What are all these parameters?!
* How do we pick the transcriptome/genome?
* Why is it so slow?
* What is different about mapping RNAseq reads vs mapping genomic reads?

Links:

* `TopHat manual <http://ccb.jhu.edu/software/tophat/manual.shtml>`__
* `Illumina iGenomes project <http://cufflinks.cbcb.umd.edu/igenomes.html>`__

Evaluating the mapping
----------------------

Check out the details::

   less tophat_all/align_summary.txt

Build a new transcriptome ("ab initio") from the combined reads using Cufflinks
-------------------------------------------------------------------------------

Now that we've mapped the reads, let's put them all together into exons
and gene models::

   module load cufflinks/2.2.1

   cufflinks -o cuff_all tophat_all/accepted_hits.bam

.. @@ cufflinks diagram

Questions:

* What exactly is Cufflinks doing?

Merge the new transcriptome with the existing reference transcriptome
---------------------------------------------------------------------

We already have some decent gene models; let's merge our new and the old ones::

   ls -1 cuff_all/transcripts.gtf > cuff_list.txt

   cuffmerge -g $HOME/RNAseq-semimodel/reference/Gallus_gallus/UCSC/galGal3/Annotation/Genes/genes.gtf \
       -o cuffmerge_all \
       -s $HOME/RNAseq-semimodel/reference/Gallus_gallus/UCSC/galGal3/Sequence/WholeGenomeFasta/genome.fa \
       cuff_list.txt

Do some cleanup::

   curl -O http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/remove-nostrand.py
   python remove-nostrand.py cuffmerge_all/merged.gtf > cuffmerge_all/nostrand.gtf

Questions:

* why do you want to merge?
* why would you have a list of more than one thing in list.txt?
* come to think of it, why aren't you (re)mapping all your reads every time?
* what's with the 'remove nostrand' script?

Extracting your new transcriptome sequences
-------------------------------------------

To get a look at the actual DNA sequences, do::

   gffread -w cuffmerge_all.fa \
           -g $HOME/RNAseq-semimodel/reference/Gallus_gallus/UCSC/galGal3/Sequence/WholeGenomeFasta/genome.fa \
           cuffmerge_all/nostrand.gtf

Questions:

* What's the difference between a GTF file and the FA file?

Checking out your new transcriptome
-----------------------------------

Take a look at the top of your FASTA file::

   head -30 cuffmerge_all.fa

Head on over to `the chicken genome browser <http://genome.ucsc.edu/cgi-bin/hgTracks?db=galGal4>`__ and try BLATing the sequence!

:doc:`s-tophat-round2`

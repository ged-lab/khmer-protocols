Mapping reads to the transcriptome with TopHat
==============================================

Now that we have some quality-controlled reads, we're going to *map* the
reads to the reference gene set, for the purpose of counting how many
reads have come from each gene.  We'll be using the `TopHat software
<http://ccb.jhu.edu/software/tophat/manual.shtml>`__

For this purpose, we've already installed the human reference gene set
on the HPC (as part of the data you loaded at the beginning).  In this
case we've loaded in the `Illumina iGenomes project
<http://cufflinks.cbcb.umd.edu/igenomes.html>`__ into the RNAseq-model
data set.

Mapping reads
-------------

Load the TopHat software::

   module load TopHat2/2.0.12

And now run TopHat::

   cd ~/rnaseq
   tophat -p 4 \
       -G ~/RNAseq-model/Homo_sapiens/Ensembl/GRCh37/Annotation/Genes/genes.gtf \
       --transcriptome-index=$HOME/RNAseq-model/transcriptome \
       -o tophat_salivary_repl1 \
       ~/RNAseq-model/Homo_sapiens/Ensembl/GRCh37/Sequence/Bowtie2Index/genome \
       salivary_repl1_R1.qc.fq.gz salivary_repl1_R2.qc.fq.gz 

This will take about 15 minutes.

Questions:

* What are all these parameters?!
* How do we pick the transcriptome/genome?
* Why is it so slow?
* What is different about mapping RNAseq reads vs mapping genomic reads.

Links:

* `TopHat manual <http://ccb.jhu.edu/software/tophat/manual.shtml>`__
* `Illumina iGenomes project <http://cufflinks.cbcb.umd.edu/igenomes.html>`__

Counting mapped reads percentage
--------------------------------

Let's ask samtools for the total number of reads that mapped::

    samtools view -c -F 4 tophat_salivary_repl1/accepted_hits.bam

You should get around 179,312.  

If we look at the `FastQC report
<http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/salivary_repl1_R1.qc_fastqc.html>`__,
we can see (at the top) that the total number of reads in the R1 file
is 95,236 (which is the same number of reads as in the R2 file, because
we only looked at the paired reads that came out of Trimmomatic).

In total, that's 190,472 reads in your trimmed (QC) data, and 179,312
mapped -- about 94%.  That's pretty good!

----

Next: :doc:`m-htseq`

Mapping reads to the transcriptome with TopHat
==============================================

Now that we have some quality-controlled reads and a new reference
transcriptome, we're going to map the
reads to the reference genome, **using the new reference transcriptome**.
We'll again be using the `TopHat software
<http://ccb.jhu.edu/software/tophat/manual.shtml>`__

Mapping reads
-------------

Load the TopHat software, if you haven't already::

   module load TopHat2/2.0.12

And now run TopHat::

   cd ~/rnaseq
   tophat -p 4 \
       -G cuffmerge_all/nostrand.gtf \
       --transcriptome-index=$HOME/RNAseq-semimodel/tophat/merged \
       -o tophat_female_repl1 \
       ~/RNAseq-semimodel/reference/Gallus_gallus/UCSC/galGal3/Sequence/Bowtie2Index/genome \
       female_repl1_R1.qc.fq.gz female_repl1_R2.qc.fq.gz 

This will take about 15 minutes.

Questions:

* How do we pick the transcriptome/genome?

Viewing the mapped reads percentage
-----------------------------------

Let's look at these numbers specifically::

   less tophat_female_repl1/align_summary.txt

Making gene counts
------------------

Now that we know which reads go with which gene, we'll use
`htseq-count <http://www-huber.embl.de/users/anders/HTSeq/doc/count.html>`__.

First, load the PySAM and HTSeq software packages::

   module load HTSeq/0.6.1

And next, run HTSeq::

   htseq-count --format=bam --stranded=no --order=pos \
       tophat_female_repl1/accepted_hits.bam \
       cuffmerge_all/nostrand.gtf > female_repl1_counts.txt

When this is done, type::

   less female_repl1_counts.txt

(again, use 'q' to exit).  These are your gene counts.

Note, these are *raw* gene counts - the number of reads that map to
each feature (gene, in this case).  They are not normalized by length
of gene. According to `this post on seqanswers
<http://seqanswers.com/forums/archive/index.php/t-9998.html>`__, both
DEseq and edgeR want exactly this kind of information!

Questions:

* what are the 'TCONS...' names?
* what do these parameters mean?
* what parameters does HTSeq take?
* why are we using so many programs?

Links:

* `HTSeq <http://www-huber.embl.de/users/anders/HTSeq/doc/overview.html>`__
* `htseq-count documentation <http://www-huber.embl.de/users/anders/HTSeq/doc/count.html>`__

Next: :doc:`s-more-tophat`

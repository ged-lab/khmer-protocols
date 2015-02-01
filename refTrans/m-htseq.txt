Counting the reads that map to each gene
========================================

Now that we know which reads go with which gene, we'll use
`htseq-count <http://www-huber.embl.de/users/anders/HTSeq/doc/count.html>`__.

First, load the PySAM and HTSeq software packages::

   module load PySAM/0.6
   module load HTSeq/0.6.1

And next, run HTSeq::

   htseq-count --format=bam --stranded=no --order=pos \
       tophat_salivary_repl1/accepted_hits.bam \
       ~/RNAseq-model/Homo_sapiens/Ensembl/GRCh37/Annotation/Genes/genes.gtf > salivary_repl1_counts.txt

When this is done, type::

   less salivary_repl1_counts.txt

(again, use 'q' to exit).  These are your gene counts.

Note, these are *raw* gene counts - the number of reads that map to
each feature (gene, in this case).  They are not normalized by length
of gene. According to `this post on seqanswers
<http://seqanswers.com/forums/archive/index.php/t-9998.html>`__, both
DEseq and edgeR want exactly this kind of information!

Questions:

* what are the 'ENS*...' names?
* what do these parameters mean?
* what parameters does HTSeq take?
* why are we using so many programs?

Links:

* `HTSeq <http://www-huber.embl.de/users/anders/HTSeq/doc/overview.html>`__
* `htseq-count documentation <http://www-huber.embl.de/users/anders/HTSeq/doc/count.html>`__

Next: :doc:`m-more-tophat`

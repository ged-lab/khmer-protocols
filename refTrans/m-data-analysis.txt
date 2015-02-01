Data analysis & differential expression
=======================================

We'll be using `edgeR
<http://www.bioconductor.org/packages/release/bioc/html/edgeR.html>`__
to do the basic differential expression analysis of our counts.

To run edgeR, you need to write a data loading and manipulation script
in R.  In this case, I've provided one -- `lung_saliva.R
<https://github.com/ngs-docs/2014-msu-rnaseq/blob/master/files/lung_saliva.R>`__.
This script will load in two samples with two replicates, execute an
MA plot, and provide a spreadsheet with differential expression
information in it.  To download it, `click here
<http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/lung_saliva.R>`__.

Links:

* `False Discovery Rate <http://en.wikipedia.org/wiki/False_discovery_rate>`__
* `Learn R with Swirl <http://swirlstats.com/>`__
* `Data Carpentry <http://www.datacarpentry.org/>`__

Running edgeR on a data subset
------------------------------

To run the script on the HPC, download the script and data files for
the 100k read subsets.  At the command line, do::

   cd ~/rnaseq
   curl -O http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/lung_saliva.R
   curl -O http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/subset/lung_repl1_counts.txt
   curl -O http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/subset/lung_repl2_counts.txt
   curl -O http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/subset/salivary_repl1_counts.txt
   curl -O http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/subset/salivary_repl2_counts.txt

Note: the ``saliva_repl1_counts.txt`` and ``lung_repl1_counts.txt``
are the files produced by :doc:`m-tophat` and :doc:`m-more-tophat`,
respectively.  I've also run them on the replicate data sets, which
produced the *repl2_counts.txt files.

Next, to run the R script, do::

   module load R
   Rscript lung_saliva.R

This will produce two files, `edgeR-MA-plot.pdf
<http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/subset/edgeR-MA-plot.pdf>`__
and `edgeR-lung-vs-salivary.csv
<http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/subset/edgeR-lung-vs-salivary.csv>`__;
they will be in your ``rnaseq`` folder in your home directory
on the HPC.  The CSV file can be opened directly in Excel; you can
also look at it `here
<https://raw.githubusercontent.com/ngs-docs/2014-msu-rnaseq/master/files/subset/edgeR-lung-vs-salivary.csv>`__.
It consists of five columns: gene name, log fold change, P-value, and
FDR-adjusted P-value.

Links:

* `edgeR tutorial from UT Austin <https://wikis.utexas.edu/display/bioiteam/Differential+gene+expression+analysis#Differentialgeneexpressionanalysis-Optional:edgeR>`__

Functional and network analysis on differentially expressed genes
-----------------------------------------------------------------

There are a number of sites that let you analyze gene lists, including
`DAVID <http://david.abcc.ncifcrf.gov/>`__.  I have gotten a number of
recommendations for `PANTHER <http://www.pantherdb.org/>`__.  PANTHER
lets you upload gene lists and explore their functional categories
interactively or in a gene list/annotation format.

More specifically, you can explore your RNAseq data with

* functional classifications, in pie chart or in list;
* tests for statistical overrepresentation;
* tests for statistical enrichment based on associated fold change.

You need to crop and transform the data a little bit before using the
functional classification.  The steps are:

1. Download `the CSV file <https://raw.githubusercontent.com/ngs-docs/2014-msu-rnaseq/master/files/subset/edgeR-lung-vs-salivary.csv>`__.  If you've produced your own, copy it over from the HPC.

2. Open it in Excel.

3. Choose an FDR cutoff (suggest FDR < 0.05 or lower) and delete all the rows after that (or, copy the rows into a new spreadsheet -- might be quicker).

4. Save as a "Tab-delimited text."  (Note, on Mac OS X, you may need to save this as "Windows formatted text" instead.)

This is now a file you can upload to PANTHER.

(`You can download my copy of this here <http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/subset/edgeR-panther-upload.txt>`__)

To do the analysis, go to http://www.pantherdb.org/. In box 1, select "Choose file"
and find the CSV file you want to upload to PANTHER.  Nothing else in box
one should be changed.

In box 2, select "Homo sapiens."

In box 3, select either "Functional analysis classification viewed in
gene list" or "Functional analysis classification viewed in pie
chart."

Click submit.

Now you can explore the results!

Links:

* `PANTHER database <http://www.pantherdb.org/>`__

Next: :doc:`m-hpc-qsub`

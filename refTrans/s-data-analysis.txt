Data analysis & differential expression
=======================================

We'll be using `edgeR
<http://www.bioconductor.org/packages/release/bioc/html/edgeR.html>`__
to do the basic differential expression analysis of our counts.

To run edgeR, you need to write a data loading and manipulation script
in R.  In this case, I've provided one -- `chick.R
<https://github.com/ngs-docs/2014-msu-rnaseq/blob/master/files/chick.R>`__.
This script will load in two samples with two replicates, execute an
MA plot, do an MDS analysis/plot, and provide a spreadsheet with
differential expression information in it.  To download it, `click
here
<http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/chick.R>`__.

Links:

* `False Discovery Rate <http://en.wikipedia.org/wiki/False_discovery_rate>`__
* `Learn R with Swirl <http://swirlstats.com/>`__
* `Data Carpentry <http://www.datacarpentry.org/>`__

Running edgeR on a data subset
------------------------------

To run the script on the HPC, download the script and data files for
the 100k read subsets.  At the command line, do::

   cd ~/rnaseq
   curl -O http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/chick.R
   curl -O http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/chick-subset/female_repl1_counts.txt
   curl -O http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/chick-subset/female_repl2_counts.txt
   curl -O http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/chick-subset/male_repl1_counts.txt
   curl -O http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/chick-subset/male_repl2_counts.txt

These various .txt files are produced by :doc:`s-tophat-round2`, :doc:`s-more-tophat`, and :doc:`s-even-more-tophat`.

Next, to run the R script, do::

   module load R
   Rscript chick.R

This will produce three files, `chick-edgeR-MA-plot.pdf
<http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/chick-subset/chick-edgeR-MA-plot.pdf>`__
`chick-edgeR-MDS.pdf
<http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/chick-subset/chick-edgeR-MDS.pdf>`__
and `chick-edgeR.csv
<http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/subset/chick-edgeR.csv>`__;
they will be in your ``rnaseq`` folder in your home directory
on the HPC.  The CSV file can be opened directly in Excel; you can
also look at it `here
<https://raw.githubusercontent.com/ngs-docs/2014-msu-rnaseq/master/files/chick-subset/chick-edgeR.csv>`__.
It consists of five columns: gene name, log fold change, P-value, and
FDR-adjusted P-value.

Links:

* `edgeR tutorial from UT Austin <https://wikis.utexas.edu/display/bioiteam/Differential+gene+expression+analysis#Differentialgeneexpressionanalysis-Optional:edgeR>`__

Next: :doc:`m-advice`

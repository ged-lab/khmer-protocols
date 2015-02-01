Miscellaneous advice
====================

Sequencing depth and number of samples
--------------------------------------

`Hart et al. (2013)
<http://www.ncbi.nlm.nih.gov/pmc/articles/PMC3842884/>`__ provides a
nice description and a set of tools for estimating your needed
sequencing depth and number of samples.  They provide an `Excel based
calculator
<http://www.ncbi.nlm.nih.gov/pmc/articles/PMC3842884/bin/supp_data.zip>`__
for calculating number of samples.  Their numbers are surprisingly
large to me ;).

In a proposal for an exploratory effort to discover differentially
expressed genes, I would suggest 3-5 biological replicates with 30-50
million reads each.  More reads is usually cheaper than more replicates,
so 50-100m reads may give you more power to resolve smaller fold changes.

Downloading your data
---------------------

If you do your sequencing at the MSU Core Facility, you'll get an e-mail
from them when you're samples are ready.  The e-mail will give you an
FTP site, a username, and a password, as well as a URL.  You can
use these to download your data.  For example, if you get::

   hostname:       titan.bch.msu.edu
   username:       rnaseqmodel
   password:       QecheJa6

   URI:            ftp://rnaseqmodel:QecheJa6@titan.bch.msu.edu

you can go to ftp://rnaseqmodel:QecheJa6@titan.bch.msu.edu in your
Web browser; that is, it lets you combine your username and password
to open that link.

In this case, you will see a 'testdata' directory.  If you click on that,
you'll see a bunch of fastq.gz files.  These are the files that you
want to get onto the HPC.

To download these files onto the HPC, log into the HPC, go to the
directory on the HPC you want to put the files in, and run a 'wget' --
for example, on the HPC::

   mkdir ~/testdata
   cd ~/testdata

   wget -r -np -nH ftp://rnaseqmodel:QecheJa6@titan.bch.msu.edu/testdata/

This will download _all_ of the files in that directory.  You can also do
them one at a time, e.g. to get 'Ath_Mut_1_R1.fastq.gz', you would do

   wget ftp://rnaseqmodel:QecheJa6@titan.bch.msu.edu/testdata/Ath_Mut_1_R1.fastq.gz

Tada!

Developing your own pipeline
----------------------------

Even if all you plan to do is change the filenames you're operating on,
you'll need to develop your own analysis pipeline.  Here are some tips.

1. Start with someone else's approach; don't design your own.  There
   are lots of partly done examples that you can find on the Web,
   including in this tutorial.

2. Generate a data subset (the first few 100k reads, for example).

2. Run commands interactively on an HPC dev node until you get all of
   the commands basically working; track all of your commands in a
   Word document or some such.

3. Once you have a set of commands that seems to work on small data,
   write a script.  Run the script on the small data again; make sure
   that works.

4. Turn it into a qsub script (making sure you're in the right 
   directory, have the modules loaded, etc.)

5. Make sure the qsub script works on your same small data.

6. Scale up to a big test data set.

7. Once that's all working, SAVE THE SCRIPT SOMEWHERE.  Then,
   edit it to work on all your data sets (you may want to make subsets
   again, as much as possible).

8. Provide your scripts and raw counts files as part of any publication
   or thesis, perhaps via `figshare <http://figshare.com>`__.

Next: :doc:`more-resources`

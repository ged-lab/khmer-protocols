Submitting jobs to the MSU HPC queue
====================================

In :doc:`m-more-tophat`, we showed you a *shell script*, which was a way
of telling the computer to do multiple things in a row.  We discussed
several advantages to scripting --

1. It automates long-running processes;
2. It tracks what you did, and you can edit it (to modify analyses) and
   also copy it (to do a family of analyses);
3. You can also provide it as part of your Methods in your paper;

There is also a fourth advantage, or really a necessity, to scripting:
it's how you use the MSU HPC to run your computation.  Briefly, to run
a job on the HPC, you create a shell script and then run 'qsub'.

Here are four scripts (plus a common library script) that you could use
to run some analyses on the HPC.  To run, do something like this::

   module load powertools
   getexample RNAseq-model

   mkdir ~/rnaseq
   mkdir ~/rnaseq/script
   cd ~/rnaseq/script
   ln -fs ~/RNAseq-model/data/*.fastq .

   curl -O http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/subset/env.sh
   curl -O http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/subset/process-1.sh
   curl -O http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/subset/process-2.sh
   curl -O http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/subset/process-3.sh
   curl -O http://2014-msu-rnaseq.readthedocs.org/en/latest/_static/subset/process-4.sh

   qsub process-1.sh
   qsub process-2.sh
   qsub process-3.sh
   qsub process-4.sh

You can use 'qstat | grep <username>' to check on your jobs' status.

Next: :doc:`m-advice`

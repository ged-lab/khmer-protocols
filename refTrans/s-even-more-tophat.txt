Using the HPC to run jobs in parallel
=====================================

Type::

   cat > chick-tophat-3.sh <<EOF

and then paste this in::

   #PBS -l walltime=24:00:00,nodes=1:ppn=2,mem=10gb
   #PBS -A classbuyin
   module load TopHat2/2.0.12
   module load HTSeq/0.6.1

   # go to the 'rnaseq' directory in my home directory
   cd ~/rnaseq

   # now run Tophat!
   tophat \
       -G cuffmerge_all/nostrand.gtf \
       --transcriptome-index=\$HOME/RNAseq-semimodel/tophat/merged \
       -o tophat_male_repl1 \
       ~/RNAseq-semimodel/reference/Gallus_gallus/UCSC/galGal3/Sequence/Bowtie2Index/genome \
       male_repl1_R1.qc.fq.gz male_repl1_R2.qc.fq.gz 

   htseq-count --format=bam --stranded=no --order=pos \
       tophat_male_repl1/accepted_hits.bam \
       cuffmerge_all/nostrand.gtf > male_repl1_counts.txt
       
   EOF

Next, do it for male_repl2::

   cat > chick-tophat-4.sh <<EOF

and then paste this in::

   #PBS -l walltime=24:00:00,nodes=1:ppn=2,mem=10gb
   #PBS -A classbuyin
   module load TopHat2/2.0.12
   module load HTSeq/0.6.1

   # go to the 'rnaseq' directory in my home directory
   cd ~/rnaseq

   # now run Tophat!
   tophat \
       -G cuffmerge_all/nostrand.gtf \
       --transcriptome-index=\$HOME/RNAseq-semimodel/tophat/merged \
       -o tophat_male_repl2 \
       ~/RNAseq-semimodel/reference/Gallus_gallus/UCSC/galGal3/Sequence/Bowtie2Index/genome \
       male_repl2_R1.qc.fq.gz male_repl2_R2.qc.fq.gz 

   htseq-count --format=bam --stranded=no --order=pos \
       tophat_male_repl2/accepted_hits.bam \
       cuffmerge_all/nostrand.gtf > male_repl2_counts.txt
       
   EOF

To submit these to the queue, do::

   qsub chick-tophat-3.sh
   qsub chick-tophat-4.sh

Next: doc:`s-data-analysis`

Processing another sample with TopHat and HTSeq
===============================================

Let's script this!

Type::

   cd ~/rnaseq/
   module load Trimmomatic/0.32
   cat > lung-tophat.sh <<EOF

and then paste this in::

   module load Trimmomatic/0.32
   module load TopHat2/2.0.8b
   module load PySAM/0.6
   module load HTSeq/0.6.1

   # go to the 'rnaseq' directory in my home directory
   cd ~/rnaseq

   # subset the data sets (100,000 reads) - you don't want to do this
   # on real data :)
   head -400000 ~/RNAseq-model/data/ERR315326_1.fastq | gzip > lung_repl1_R1.fq.gz
   head -400000 ~/RNAseq-model/data/ERR315326_2.fastq | gzip > lung_repl1_R2.fq.gz

   # run Trimmomatic.  Here, the inputs are 'lung_repl1_R1.fq.gz' and
   # 'lung_repl1_R2.fq.gz', and the outputs are 'lung_repl1_R1.qc.fq.gz'
   # and 'lung_repl1_R2.qc.fq.gz'.
   java -jar \$TRIM/trimmomatic PE lung_repl1_R1.fq.gz lung_repl1_R2.fq.gz \
     lung_repl1_R1.qc.fq.gz s1_se lung_repl1_R2.qc.fq.gz s2_se \
     ILLUMINACLIP:\$TRIM/adapters/TruSeq2-PE.fa:2:40:15 \
     LEADING:2 TRAILING:2 \                            
     SLIDINGWINDOW:4:2 \
     MINLEN:25

   # now run Tophat!
   # The inputs are the outputs of the previous Trimmomatic step.
   # The outputs are going to be under the 'tophat_lung_repl1' directory.
   tophat -p 4 \
       -G ~/RNAseq-model/Homo_sapiens/Ensembl/GRCh37/Annotation/Genes/genes.gtf \
       --transcriptome-index=$HOME/RNAseq-model/transcriptome \
       -o tophat_lung_repl1 \
       ~/RNAseq-model/Homo_sapiens/Ensembl/GRCh37/Sequence/Bowtie2Index/genome \
       lung_repl1_R1.qc.fq.gz lung_repl1_R2.qc.fq.gz 

   # count the hits by gene -- 'tophat_lung_repl1' is the main output,
   # from Tophat.
   htseq-count --format=bam --stranded=no --order=pos tophat_lung_repl1/accepted_hits.bam \
       ~/RNAseq-model/Homo_sapiens/Ensembl/GRCh37/Annotation/Genes/genes.gtf > lung_repl1_counts.txt
   EOF

(Be sure to press the Enter or Return key after pasting this in!)  This is
`called a 'heredoc' <http://en.wikipedia.org/wiki/Here_document#Unix-Shells>`__
and it gives a way to write a shell script via copy-paste ;).

Next, type::

   bash lung-tophat.sh

This will run all of the commands in the file 'lung-tophat.sh'.

You can use the 'nano' editor to modify this file -- type::

   nano lung-tophat.sh

Next: :doc:`m-data-analysis`

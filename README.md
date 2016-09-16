## fastq-tools

---
###filter fastq
I will store any NGS manipulation scripts in this repository.

remove_reads.py and filterFastq/filterFastqC are both tools for filtering fastq files with unwanted read IDs. 

	usage: bin/filterFastq [options]

	[options]
	-q    <fastq file>
	-i    <idFile>
	-v    inverted match (same as grep -v)

	usage: scripts/remove_reads.py <fastqFile> <idFile>    

* \<fastqFile\> can be any four line fastq sequencing files
	* line 1: id
	* line 2: sequence
	* line 3:  + / +id
	* line 4: quality scores
* \<idFile\> sotres one id per line.

Required: [gzstream library](http://www.cs.unc.edu/Research/compgeom/gzstream/)

---
Benchmarking:
For a Fastq file with 8720139 reads, id file with 4477244 ids:


	$ time ./filterReads fastqFile  idFile | grep -c '^@'
	## 4242895
	## ./filterReads fastqFile
	## 18.07s user
	## 1.89s system
	## 99% cpu
	## 20.154 total

	$ time ./remove_reads.py fastqFile  idFile | grep -c '^@'
	## 4242895
	## ./remove_reads.py fastqFile
	## 62.37s user
	## 2.81s system
	## 99% cpu
	## 1:05.44 total

Major difference, filterReads.c hashed id list using [seqtk library](https://github.com/lh3/seqtk.git) instead of fastq file when comparing to remove_reads.py

updated:
I have combined **filterReads** and **extractSeq** into **filterFastq**. Switching between exlusion mode and inclusion mode is done by adding -v flag.


---
Blog post: [python script](http://wckdouglas.github.io/mediator/feature/2015/03/18/fastq_extract.html)		
Updated blog post: [C program and comparison](http://wckdouglas.github.io/mediator/feature/2015/04/24/fastq_extract-updated.html)


---
###sam2fastq
sam2fastq.py is a script to extract unmapped reads from sam file and write it in fastq format.    

	usage: samtools view <mapped.bam> | scripts/sam2fastq.py - > <out.fq>

C++ version:

	usage: bin/sam2fastq <sam file>|<stdin>

	Takes in samFile:
	samtools view <bamFile> | bin/sam2fastq -

---

###splitFastq
splitFastq is a program to split fastq files from next-gen sequencing libraries. Inspired by UNIX ***split*** function. 

	usage: bin/splitFastq -i <fqfile> -n <# of record per file> -o <prefix> [-z]
	[options]
	-i    <fastq file> can be gzipped
	-n    <number of record in each splitted file> default: 10000000
	-o    <prefix>
	-z    optional: gzip output

this program aims to split reads before mapping or any analysis in order to make use of high-throughput computing and reduce run time for different type of analysis. 
Required: [gzstream library](http://www.cs.unc.edu/Research/compgeom/gzstream/)

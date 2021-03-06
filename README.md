# markerfinder
Script for annotating phylogenetic marker proteins for concatenated protein phylogenies.  

This script will search a file of proteins against a set of 40 curated Hidden Markov Models for phylogenetic marker genes. These markers are useful for phylogenetic analysis of both bacteria and archaea, and they have been previously described in Sunagawa et al., Nat. Methods, 2013 (https://doi.org/10.1038/nmeth.2693).

The program assumes protein IDs are provided in the Prodigal format (i.e., contigname_1, contigname_2, etc). 
The program requires HMMER3 (in your PATH) and the SeqIO package of Biopython. 

The program will search for fragmented RNA Polymerase subunits and join them in silico for concatenated alignments. These genes are sometimes fragmented in cyanobacteria, thermophilic archaea, and other microbial lineages, which can cause problems for proper annotation and incorporation into concatenated alignments. Proteins are only joined if they are encoded on the same contig/replicon and have non-overlapping HMMER alignments to the COG0085 or COG0086 HMMs. 

There are options that allow for slightly different marker gene sets to be used. 

all: all 40 marker genes

ribo: only 27 ribosomal proteins

rnap: only 3 RNAP subunits

ribo_rnap: 27 ribosomal proteins and 3 RNAP subunits


#### Example usage 1: python markerfinder.py -i <directory of protein .faa files> -p testproject -t 2

This will use 2 threads and use all 40 marker genes

#### Example usage 2: python markerfinder.py -i <directory of protein .faa files> -p testproject -t 2 -db ribo_rnap

This will use 2 threads and use only 27 ribosomal proteins and 3 RNAP subunits


#### Output files
markerfinder.py provides several output files:

testproject.summary.tsv         This is a tab-delimited output file that provides the annotation results. 

testproject.proteins.faa  This is the protein file with all proteins with best hits to the HMMs. Proteins are re-named to incorporate their annotation.

raw_output.txt          This is the parsed raw HMMER3 output. It can be used as a reference for debugging. 

testproject.cogs.txt                This is a cogs-formatted file that, together with the marerfinder_proteins.faa file, can be used as input for an ETE3 species tree workflow 
(http://etetoolkit.org/documentation/ete-build/).


#### to continue on and make a concatenated alignment

If you want to make a concatenated alignment you have two main options- 1) take the output files from markerfinder.py and use them as input for the ETE3 toolkit, or 2) use the get_alignment.py script to align and concatenate the marker proteins found with markerfinder.py. 

get_alignment.py requires Clustal Omega in your PATH.

#### Example usage: python get_alignment.py testproject.proteins.faa testproject.cogs.txt concat_alignment.aln

This will create the concat_alignment.aln file, which is a concatented alignment of all markers used by markerfinder.py. Note that this alignment is not trimmed in any way, so you probably want to do that afterwards using trimAl or a similar program. This script will also create a folder called "alignments" with the individually aligned markers, in case you want to inspect those alignments. If genes are missing, in the case of incomplete genomes, the protein sequence is replaced with a string of Xs in that portion of the alignment.  


#### starting from nucleotide files
If you are starting this whole process with nucleotide files you can predict proteins first with the prodigal_launcher.py script. This is just a simple script that goes through a folder of .fna or .fa nucleotide fasta files and predicts proteins. 

#### Example usage: python prodigal_launcher.py nucl_folder prot_folder



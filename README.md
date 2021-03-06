Duplex Sequencing
=================

Duplex Sequencing software
Version 3.0
July 11, 2016
Programs by Scott Kennedy(1)
(1) Department of Pathology, University of Washington School of Medicine, Seattle, WA 98195  

####It is strongly recommended to use UnifiedConsensusMaker.py, as it is faster and easier to use.
####The version outlined in [Kennedy et al 2014](http://www.ncbi.nlm.nih.gov/pubmed/25299156) can be found in the Nat_Protocols_Version directory

## Glossary

### Single Stranded Consensus Sequence (SSCS)

A construct created by comparing multiple reads and deciding ambiguities by
simple majority.

### Duplex Consensus Sequence (DCS)

A construct created by comparing two SSCSs.

### Family

A group of reads that shares the same tag sequence.

## Summary of process

UnifiedConsensusMaker is meant to result in the transformation of
an unaligned *.bam file derived from data obtained on an
Illumina sequencing run into two final *.fastq files that have been
collapsed into their final Duplex Consensus Sequences (DCS).
This workflow will also generate a file containing a list of every tag 
that is present and how many times it occurred, as well as a file 
containing SSCSs that didn't have a mate and
were unable to make a DCS (extraConsensus.bam).

## Dependencies

The following programs and packages must be installed on your computer.

Package       | Written with version
------------- | --------------------
Samtools      | >=1.3.1
Python        | 2.7.X
Pysam         | >=0.9.0
MatPlotLib    | >=1.5.1 (optional)

## Input
UnifiedConsensusMaker.py takes an unaligned bam file generated by [Picard
FastqToSam](https://broadinstitute.github.io/picard/command-line-overview.html). Picard version >2.2.X is known to work, but earlier versions 
are likely to work, as well.

## Usage

python UnifiedConsensusMaker.py --input <i>unaligned_bam_file.bam</i> --prefix <i>name</i>

Default values are in brackets

Arguments:
  -h, --help            show this help message and exit
  
  --input IN_BAM        Path to unaligned, paired-end, bam file.
  
  --taglen TAG_LEN      Length in bases of the duplex tag sequence.[12]
  
  --spacerlen SPCR_LEN  Length in bases of the spacer sequence between duplex
                        tag and the start of target DNA. [5]
                        
  --tagstats            output tagstats file
  
  --minmem MINMEM       Minimum number of reads allowed to comprise a
                        consensus. [3]
                        
  --maxmem MAXMEM       Maximum number of reads allowed to comprise a
                        consensus. [200]
                        
  --cutoff CUTOFF       Percentage of nucleotides at a given position in 
                        a read that must be identical in order for a 
                        consensus to be called at that position. [0.7]
                        
  --Ncutoff NCUTOFF     With --filt 'n', maximum fraction of Ns allowed 
                        in a consensus [1.0]
                        
  --write-sscs          Print the SSCS reads to file in FASTQ format
                        [False]
                        
  --without-dcs         Don't print final DCS reads [False]
  
  --rep_filt REP_FILT   Remove tags with homomeric runs of nucleotides of
                        length x. [9]
                        
  --prefix PREFIX       Sample name to uniquely identify samples that 
                        will be appended as a prefix to the output files [None]

Required arguments are --input and --prefix.

## Data Outputs

Default output are two fastq files consisting of the final DCS
with the reads in the read 1 and read 2 fastq files being in register for proper
paired-end analysis. The header line of each entry consists of the tag in alpha/beta
or beta/alpha format (see [Schmitt et al 2012](http://www.ncbi.nlm.nih.gov/pubmed/22853953) and [Kennedy et al 2014](http://www.ncbi.nlm.nih.gov/pubmed/25299156) for details) 
such that the first half of the tag is always alphanumerically "less than" the
second half. The third line indicates the number of reads that make up the SSCS that
go on to form the final DCS. UnifiedConsensusMaker.py also calculates the final 
quality score for each position based on the quality scores of the underlying raw
reads. If a quality score is found to be >Q40 (almost all are), the quality score
is capped at 'J', which is the highest quality score currently supported by Illumina.

If --write-sscs is invoked, two fastq files are produced, one for each read. 
These reads are in register for paired-end sequencing and can be directly aligned 
using the aligner of your choice. Similarly to the DCS, the quality score is
calculated from the raw reads and capped at a maximum value of Q40.

If --tagstats is invoked, a plots of the family size and the correlation between
the family sizes that make up a DCS are generated.  This option requires that 
MatPlotLib be installed.


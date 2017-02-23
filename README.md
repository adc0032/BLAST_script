# BLAST_script
BLAST stands for Basic Local Alignment Search Tool. There are quite a few different types of blast options: 
- BLASTN: Nucleotide
- BLASTP: Protein 
- And less common BLASTX, TBLASTN, and TBLASTX

BLASTN, which is what is used in this spript, uses a nucleotide query and a nucleotide database. It matches oligos to a genome.
A blast output generally provides raw scores, and Expect Value (E-value), and a list of alignments. However, there are many ways to limit the output, all of which can be found using `blastn --help`. 

Output Options: 
> Alignment view options:  
> 0 = Pairwise  
> 1 = Query-anchored showing identities  
> 2 = Query-anchored no identities  
> 3 = Flat query-anchored showing identities  
> 4 = Flat query-anchored no identities  
> 5 = BLAST XML  
> 6 = Tabular  
> 7 = Tabular with comment lines  
> 8 = Seqalign (Text ASN.1)  
> 9 = Seqalign (Binary ASN.1)    
> 10 = Comma-separated values  
> 11 = BLAST archive (ASN.1)  
> 12 = Seqalign (JSON)  
> 13 = Multiple-file BLAST JSON  
> 14 = Multiple-file BLAST XML2  
> 15 = Single-file BLAST JSON  
> 16 = Single-file BLAST XML2  
> 17 = Sequence Alignment/Map (SAM) 


Option 7 was used to display the results in the tabular with comments format. Two limitations were used in our script to set a  limit of one hit per query and the E-value to values less than 0.0001 (the smaller the E-value, the better):   
`-evalue` and `max_target_seqs` 

The general design of a blast from the command line is: 
`blastn -db "DATABASE1" DATABASE2"... -query "FILE.fasta" -output "OUT TYPE" -[limit output: -evalue -Max_target_seqs or sorting of results]`

Example of BLAST:   
`blastn -db "ATmt.fasta ATcp.fasta ATchrV.fasta Arabidopsis_thaliana/CHR_I/NC_003070.gbk Arabidopsis_thaliana/CHR_II/NC_003071.gbk Arabidopsis_thaliana/CHR_III/NC_003074.gbk" -query test.fasta -outfmt 7 -evalue 0.00001 -max_target-seqs 1| egrep -v '^#'| sed 's/[[:space:]]1_\/home.*NC_[0-9]*[[:space:]]/\tNT\t/'| awk '{print $1,$2}' | sort | uniq | awk '{print $2}' | sort | uniq -c | sort -n > RawCounts.txt`
In this code, the output of the number of hits from the mt, cp, and chrV fasta files were put into a file called RawCounts.txt

Finding "Dropped" sequences (No Hits):
`NUM=$(blastn -db "ATmt.fasta ATcp.fasta ATchrV.fasta Arabidopsis_thaliana/CHR_I/NC_003070.gbk Arabidopsis_thaliana/CHR_II/NC_003071.gbk Arabidopsis_thaliana/CHR_III/NC_003074.gbk" -query test.fasta -outfmt 7 -evalue 0.00001 -max_target-seqs 1 | grep -c ' 0 hits ' ) && echo No_hits $NUM >> RawCounts.txt`
This counts the number of "No Hits" that occurred during the blast, and adds it to the RawCounts.txt file.


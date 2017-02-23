# BLAST_script
BLAST stands for Basic Local Alignment Search Tool. There are quite a few different types of blast options: 
- BLASTN: Nucleotide
- BLASTP: Protein 
- And less common BLASTX, TBLASTN, and TBLASTX

BLASTN, which is what is used in this spript, uses a nucleotide query and a nucleotide database. It matches oligos to a genome.  
A blast output generally provides raw scores, and Expect Value (E-value), and a list of alignments. However, there are many ways to limit the output, all of which can be found using `blastn --help`. 

Two of these limitations were used in our script to set a  limit of one hit per query and the E-value digits:   
`-evalue` and `max_target_seqs` 

The general design of a blast from the command line is: 
`blastn -db "DATABASE1" DATABASE2"... -query "FILE.fasta" -output "OUT TYPE" -[limit output: -evalue -Max_target_seqs or sorting of results]`

Example:   
`blastn -db "ATmt.fasta ATcp.fasta ATchrV.fasta Arabidopsis_thaliana/CHR_I/NC_003070.gbk Arabidopsis_thaliana/CHR_II/NC_003071.gbk Arabidopsis_thaliana/CHR_III/NC_003074.gbk" -query test.fasta -outfmt 7 -evalue 0.00001 -max_target-seqs 1| egrep -v '^#'| sed 's/[[:space:]]1_\/home.*NC_[0-9]*[[:space:]]/\tNT\t/'| awk '{print $1,$2}' | sort | uniq | awk '{print $2}' | sort | uniq -c | sort -n > RawCounts.txt`



**Example Command:** `blastn -query test.fasta -db ATmt.fasta -outfmt [test type #] | less`
 `blastn -query test.fasta -db ATmt.fasta -outfmt 7 | less`
                 
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


##Building Databases Containing Multiple Fasta Files
**Formatting:** `-db "database 1" "database2" "database3"`       
**Example Code:** 
```javascript
blastn -db "ATmt.fasta ATcp.fasta ATchrV.fasta Arabidopsis_thaliana/CHR_I/NC_003070.gbk Arabidopsis_thaliana/CHR_II/NC_003071.gbk Arabidopsis_thaliana/CHR_III/NC_003074.gbk" -query test.fasta -outfmt 7 | less -S
```
##Limiting Output
Limit to one hit per query, and limit the e value
**Example:** 
```javascript
blastn -db "ATmt.fasta ATcp.fasta ATchrV.fasta Arabidopsis_thaliana/CHR_I/NC_003070.gbk Arabidopsis_thaliana/CHR_II/NC_003071.gbk Arabidopsis_thaliana/CHR_III/NC_003074.gbk" -query test.fasta -outfmt 7 -evalue 0.00001 -max_target_seqs 1 | less -S
```
##Counting Sequence Output
 ``` javascript
blastn -db "ATmt.fasta ATcp.fasta ATchrV.fasta Arabidopsis_thaliana/CHR_I/NC_003070.gbk Arabidopsis_thaliana/CHR_II/NC_003071.gbk Arabidopsis_thaliana/CHR_III/NC_003074.gbk" -query test.fasta -outfmt 7 -evalue 0.00001 -max_target-seqs 1| egrep -v '^#'| sed 's/[[:space:]]1_\/home.*NC_[0-9]*[[:space:]]/\tNT\t/'| awk '{print $1,$2}' | sort | uniq | awk '{print $2}' | sort | uniq -c | sort -n > RawCounts.txt
```
**Note:** We dropped one cause we got 578 in the count not 579
        
##Finding "Dropped" sequences (No Hits)
```javascript
NUM=$(blastn -db "ATmt.fasta ATcp.fasta ATchrV.fasta Arabidopsis_thaliana/CHR_I/NC_003070.gbk Arabidopsis_thaliana/CHR_II/NC_003071.gbk Arabidopsis_thaliana/CHR_III/NC_003074.gbk" -query test.fasta -outfmt 7 -evalue 0.00001 -max_target-seqs 1 | grep -c ' 0 hits ' ) && echo No_hits $NUM >> RawCounts.txt
```

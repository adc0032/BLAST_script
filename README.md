

# BLAST_script


##Step One: Locate Files
Copy all fasta files from appropraite folders to the directly that you are working in.
If copying and entire directory, use `-R` to copy the directory over 

To see what the file endings mean, look at the read me in the genomes unzipped file
        Example: `less /apps/bio/unzipped/genomes/Arabidopsis_thaliana/README`
        
To create a symbolic link to the path: 
        `ln -s /apps/bio/unzipped/genomes/Arabidopsis_thaliana/`

###Step Two: Building Databases
Load the module from blast+
Command: `makeblastdb -in FILE.fasta -dbtype TYPE_WANTED`

###Step Three: Choosing an out format

        #blastn -query test.fasta -db ATmt.fasta -outfmt [test type #] | less
            #alignment view options:
                #0 = Pairwise,
                #1 = Query-anchored showing identities,
                #2 = Query-anchored no identities,
                #3 = Flat query-anchored showing identities,
                #4 = Flat query-anchored no identities,
                #5 = BLAST XML,
                #6 = Tabular,
                #7 = Tabular with comment lines,
                #8 = Seqalign (Text ASN.1),
                #9 = Seqalign (Binary ASN.1),
                #10 = Comma-separated values,
                #11 = BLAST archive (ASN.1),
                #12 = Seqalign (JSON),
                #13 = Multiple-file BLAST JSON,
                #14 = Multiple-file BLAST XML2,
                #15 = Single-file BLAST JSON,
                #16 = Single-file BLAST XML2,
                #17 = Sequence Alignment/Map (SAM)
                blastn -query test.fasta -db ATmt.fasta -outfmt 7 | less
#How to put the databases together; setting up the first blast run using test.fasta
        blastn -query test.fasta -db ATmt.fasta -outfmt 7 >qtest_dbmt.blastn_out
        blastn -query test.fasta -db ATcp.fasta -outfmt 7 >qtest_dbcp.blastn_out

#Test Run Two: build a database including all three
        #-db "database 1" "database2" "database3"
        blastn -db "ATmt.fasta ATcp.fasta ATchrV.fasta Arabidopsis_thaliana/CHR_I/NC_003070.gbk Arabidopsis_thaliana/CHR_II/NC_003071.gbk         Arabidopsis_thaliana/CHR_III/NC_003074.gbk" -query test.fasta -outfmt 7 | less -S

        #Limit to one hit per query, and limit the e value
        blastn -db "ATmt.fasta ATcp.fasta ATchrV.fasta Arabidopsis_thaliana/CHR_I/NC_003070.gbk Arabidopsis_thaliana/CHR_II/NC_003071.gbk         Arabidopsis_thaliana/CHR_III/NC_003074.gbk" -query test.fasta -outfmt 7 -evalue 0.00001 -max_target_seqs 1 | less -S

        #counting the sequences
        blastn -db "ATmt.fasta ATcp.fasta ATchrV.fasta Arabidopsis_thaliana/CHR_I/NC_003070.gbk Arabidopsis_thaliana/CHR_II/NC_003071.gbk         Arabidopsis_thaliana/CHR_III/NC_003074.gbk" -query test.fasta -outfmt 7 -evalue 0.00001 -max_target-seqs 1| egrep -v '^#'| sed          's/[[:space:]]1_\/home.*NC_[0-9]*[[:space:]]/\tNT\t/'| awk '{print $1,$2}' | sort | uniq | awk '{print $2}' | sort | uniq -c |           sort -n > RawCounts.txt

        #we dropped one cause we got 578, not 579
        #finding the one we dropped
        NUM=$(blastn -db "ATmt.fasta ATcp.fasta ATchrV.fasta Arabidopsis_thaliana/CHR_I/NC_003070.gbk                                             Arabidopsis_thaliana/CHR_II/NC_003071.gbk Arabidopsis_thaliana/CHR_III/NC_003074.gbk" -query test.fasta -outfmt 7 -evalue               0.00001 -max_target-seqs 1 | grep -c ' 0 hits ' ) && echo No_hits $NUM >> RawCounts.txt
```


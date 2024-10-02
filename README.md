
PathSeq analysis of non-human RNA sequences
The Genome Analysis Toolkit (GATK)-PathSeq algorithm was employed to computationally subtract human sequences from the TCGA open access RNA-Seq raw counts (568 files) downloaded from the Genomic Data Commons 
Subsequently, the remaining non-human reads were aligned to pre-built microbial reference genome downloaded from broad institute website using the BWA-MEM aligner, 
facilitating the taxonomic classification of these residual sequences. 

The 'score' files contain the results from PathSeq, the way they can be downloaded and put together in a table is available on the code https://github.com/bachemma/TCGA_PathSeq.git
@id : the number id of each sample 
@tax_id :  taxonomy identifier, which is a unique numerical ID assigned to each organism or taxonomic group in the NCBI
@taxonomy :  the full taxonomic lineage of an organism, from the highest rank (such as root or kingdom) down to the sub-species level if possible 
@type : the taxon's level identied (such as genus, specis, sub-species) 
@name : the taxon's name 
@kingdom : bacteria, viruses, fongi, archaea 
@score : indicates the amount of evidence that this taxon is present, based on the number of reads that aligned to references in this taxon. 
This takes into account uncertainty due to ambiguously mapped reads by dividing their weight across each possible hit. It it also normalized by genome length
@score_normalized : the same as score, but normalized to sum to 100 within each kingdom. this can be related to the relative abundance of a given taxon in a sample 
for example : for the species Fusobacterium nucleatum, the score_normalized represents the percentage of Fusobacterium nucleatum wihtin bacteria in a sample and 
the sum of all normalized bacterial species scores in this sample is equal to 100
@reads : number of mapped reads (ambiguous or unambiguous)
@unambiguous : number of unambiguously mapped reads
@reference_length : reference length (in bases) if there is a reference assigned to this taxon. 
Unlike scores, this number is not propagated up the tree, i.e. it is 0 if there is no reference corresponding directly to the taxon. 

The 'uuid_metadata.csv' file contains the clinical data of public head and neck cancer from TCGA clinical data 
@id : same as in the 'score'files 
@NIRF : To be classified as NIRF, cases must not smoke and must not consume alcohol or only consume it in small amounts (maximum 1 drink/day).
Non-smoking cases with missing data regarding alcohol consumption were also considered as NIRF.
@cluster : four distinct clusters based on hierarchical clustering of the relative contributions of mutational signatures were previously identified (https://www.medrxiv.org/content/10.1101/2023.12.15.23299866v2)


Fonction compare_species : 
Performs statistical comparisons on species or genera across the 4 different clusters using the Kruskal-Wallis test and the Wilcoxon rank-sum test.
It first identifies unique species and clusters in the input dataset and checks for significant differences in scores between clusters for each species.
If a significant result is found in the Kruskal-Wallis test, it performs pairwise comparisons between clusters using the Wilcoxon test. 
The p-values from these comparisons are then adjusted using the Benjamini-Hochberg method to control for multiple testing. 

This function was applied only to 'primary tumor' samples (excluding normal tissues) and to tongues cancer.
Only species with a p_adjusted less than 0.1 and only the comparison between the 5mC deamin and SBS16 clusters (which contain the most tongues) were kept.
Results for species and genera are tabulated, named 'positive_genus.xls' and 'positive_species.xls' and contain : 
@name : the name of the specie or genus 
@comparaison : cluster 5mC deamin compare to SBS16 
@p_adj : p_value adjusted using BH method
@positive_sample : number of samples in this cluster 5mC deamin that are positive for this species or genus 
@sample_by_cluster : total number of samples in this cluster 5mC deamin that are positive for a specie (n=92) or genus (n=106).  
@percentage : percentage of positive samples among total number of samples 

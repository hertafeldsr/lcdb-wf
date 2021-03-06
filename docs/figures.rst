.. _figures:

"Figures" workflow
------------------

This workflow is a working example of how you would tie together output from
RNA-seq, ChIP-seq, and "external" workflows to automate figure creation and
formally link together the dependencies of each figure all the way back to the
original fastq files. If any changes are made upstream, they will trigger
downstream rules to re-run as needed.

For example, if a new GTF annotation file comes out, you would change the URL
in the config, and then re-run the figures workflow. All RNA-seq jobs that
depended on some way on the GTF file will be re-run. This will include the
feature counts and downstream RNA-seq analysis, but will *not* re-run any of
the jobs like trimming or mapping that do not depend on the GTF. In addition,
any figures that depended in some way on that GTF file will also be re-run.



To provide a sufficiently complex example that can be used in real-world
applications, this workflow currently:

    - counts the number of peaks in all configured peak-calling runs and stores
      the output in a TSV report (work is performed in the script
      ``scripts/peak_count.py``)
    - identifies peaks at promoters of genes, reports a summary of how many
      peaks from each run were found in a promoter, and creates BED files of
      these subsets (``scripts/peaks_at_promoters.py``)
    - builds the DAG image for the ChIP-seq and RNA-seq workflows
    - symlinks over the ChIP-seq peaks and RNA-seq differential expression results
    - extracts the text from the README.txt files created by the scripts
      (usually from their docstrings), and compiles them into a summary report
    - the ``figures`` directory can then be zipped up and distributed to collaborators


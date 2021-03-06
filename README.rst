Just use `Pingouin <https://github.com/raphaelvallat/pingouin>`__ instead. This package is not maintained.

pbsea: Pandas Based Singular Enrichment Analysis
================================================
.. contents:: Table of contents
   :backlinks: top
   :local:

Installation
------------

The package works on Python 3.

Using GitHub
~~~~~~~~~~~~

.. code:: sh

	pip install -e git+https://github.com/ArnaudBelcour/pbsea.git@master#egg=pbsea

Singular Enrichment Analysis
----------------------------

Analysis
~~~~~~~~

An enrichment analysis compares the occurrence of an entity in a list of interest 
to the occurence in a reference (for example the number of cat in one country compared 
to the number of cat in the world).

Two files are used : pbsea.py (Pandas Based Singular Enrichment Analysis), preprocessing.py.

In pbsea.py, three class are present:

#. PandasBasedEnrichmentAnalysis: to peform a Singular Enrichment Analysis on a pandas dataframe.
#. AnnotationEnrichmentAnalysis: to perform a Singular Enrichment Analysis on Annotation terms.
#. EnrichmentAnalysisExperimental: to perform a Singular Enrichment Analysis on everything with SgoF multiple testing correction.

In preprocessing.py, four functions are present:

#. preprocessing_files: create a pandas dataframe from two files.
#. go_translation_dictionary_creation: create a dictionary containing GO number as key and GO label as value.
#. ec_translation_dictionary_creation: create a dictionary containing EC number as key and EC name as value.
#. interpro_translation_dictionary_creation: create a dictionary containing InterPro id as key and InterPro name as value.

The analysis take two input files (with preprocessing) or a pandas dataframe,
with two columns (one for the interest values, one for the reference values and
in index the object to analyze (e.g. GO terms)).

The script performs a Singular Enrichment Analysis. This analysis
takes a list of genes (for example differentially expressed genes) and
compute an enrichment term for each annotation term in this list. For a
better definition, read the `article writed by Huang et al.
(2009) <https://academic.oup.com/nar/article-lookup/doi/10.1093/nar/gkn923>`__.

The first class ("PandasBasedEnrichmentAnalysis") is the basic method on pandas dataframe,
which computes an hypergeometric test for variables and calculates different multiple tests
corrections (Bonferroni, Holm, Sidak, Benjamini & Hochberg and SGoF).

The second class ("AnnotationEnrichmentAnalysis") inherits from
"PandasBasedEnrichmentAnalysis" and overrides a function to add label for three
annotations (GO terms, EC and InterPro domains) to the results.

The third class ("EnrichmentAnalysis") peforms an analysis and add the
SGoF multiple testing correction.

Tests used :

-  Hypergeometric test to compare the distribution of GO terms in your
   list and in the complete organism.

-  Normal approximation when using big numbers.

Multiple Testing Correction
~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  `Bonferroni
   Correction <http://www.jstor.org/stable/2282330?seq=1#page_scan_tab_contents>`__
   Dunn, Olive Jean. “Multiple Comparisons Among Means.” Journal of the
   American Statistical Association, vol. 56, no. 293, 1961, pp. 52–64.

-  `Sidak <https://www.jstor.org/stable/2283989?seq=1#page_scan_tab_contents>`__
   Sidak, Zbynek. “Rectangular Confidence Regions for the Means of
   Multivariate Normal Distributions.” Journal of the American
   Statistical Association, vol. 62, no. 318, 1967, pp. 626–633.

-  `Holm <http://www.jstor.org/stable/4615733?seq=1#page_scan_tab_contents>`__
   Holm, Sture. “A Simple Sequentially Rejective Multiple Test
   Procedure.” Scandinavian Journal of Statistics, vol. 6, no. 2, 1979,
   pp. 65–70.

-  `Benjamini &
   Hochberg <https://www.jstor.org/stable/2346101?seq=1#page_scan_tab_contents>`__
   Benjamini, Yoav, and Yosef Hochberg. “Controlling the False Discovery
   Rate: A Practical and Powerful Approach to Multiple Testing.” Journal
   of the Royal Statistical Society. Series B (Methodological), vol. 57,
   no. 1, 1995, pp. 289–300.

-  `Benjamini &
   Yekutieli <http://www.jstor.org/stable/2674075?seq=1#page_scan_tab_contents>`__
   Benjamini, Yoav, and Daniel Yekutieli. “The Control of the False
   Discovery Rate in Multiple Testing under Dependency.” The Annals of
   Statistics, vol. 29, no. 4, 2001, pp. 1165–1188.

-  `SGoF <https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2719628/>`__
   Carvajal-Rodríguez, Antonio, Jacobo de Uña-Alvarez, and Emilio
   Rolán-Alvarez. “A New Multitest Correction (SGoF) That Increases Its
   Statistical Power When Increasing the Number of Tests.” BMC
   Bioinformatics 10 (2009): 209.

Use
~~~

The input of the analysis is a pandas dataframe with two columns (occurrence in interest
and occurrence in reference) and an index (the object to analyse) like this one:

=========== ====================== =======================
index       Occurrence in interest Occurrence in reference
=========== ====================== =======================
GO:00000001 2                      4
GO:00000002 10                     20
=========== ====================== =======================

For GO terms, EC and InterPro id you can use one of the function of translation dictionary creation
to obtain a dictionary allowing you to translate the id into name.

.. code:: python

    from pbsea import PandasBasedEnrichmentAnalysis

    number_gene_interest = 5
    number_gene_reference = 6700
    alpha = 0.05
    normal_approximation_threshold = 100000

    analysis = PandasBasedEnrichmentAnalysis(dataframe, 'Occurrence in interest',
                            'Occurrence in reference', number_gene_interest, number_gene_reference,
                            alpha, normal_approximation_threshold)
    result_dataframe = analysis.enrichment_analysis()

The result will be a pandas dataframe.

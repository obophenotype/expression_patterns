## Aims

Expression data can take many forms: assertions about the location of expression via annotations using ontology terms; expression pattern images; images of single labelled cells or clones of cells that make up some part of an expression pattern; RNA seq data for identified tissues, groups of cells or single cells.

The aim of this site is to document semantic patterns that can be used to relate annotations to images, samples or drawn from papers so that the data from all of them can be integrated into a queryable whole.

### Example use cases:

* Querying for genes expressed in some specified structure
* Reporting where a gene is expressed
* Finding images of the expression pattern of some specified gene or transgene
* Finding images of expression patterns for a specified transgene
* Finding images of expression pattern fragments.
* Querying for circuits using expression of some specific gene as a criterion
* Displaying (all?) the genes expressed in a cell, organised using GO classifications.

## Technology

The semantics here are defined using OWL 2.0, assuming an EL profile for tractability.  Some query discussusion assumes access to the data in a graph store that can be queried by graph pattern matching - triplestore, Neo4J (SciGraph or OLS as modified by VFB).

## Defining expression patterns

We distinguish between an expression pattern and a localization pattern:  

An expression pattern consists of the mereological sum of all cells expressing some specific gene product in an organism or in some specified region of an organism.  A localization pattern is the pattern of localization of a gene product at the cellular or or subcellular level. It may include non-cellular structures and structures distant from the site of expression. 

We define the relation:

* 'expresses': Y expresses X if and only if there exists a gene expression process (GO:0010467) that occurs in y and x is a gene (or transgene) that is transcribed into a transcript as part of that gene expression process.

* X ubiquitously expresses Y iff:  Y is a [gene](http://www.ebi.ac.uk/ols/ontologies/so/terms?iri=http%3A%2F%2Fpurl.obolibrary.org%2Fobo%2FSO_0000704); X is a multicellular anatomical structure; (['gene expression'](http://www.ebi.ac.uk/ols/ontologies/go/terms?iri=http%3A%2F%2Fpurl.obolibrary.org%2Fobo%2FGO_0010467) and 'has input' Y) 'occurs' in (all cells that are part of X)
   * subPropertyOf expresses  

i.e. at the cellular level, an expression pattern is binary. Note that 'expresses' applies if just one expressing cell is present. So if X and Y are multicellular structures and X overlaps Y and X ubiquitously expresses gene A then we can (reasonably safely^) infer:

* overlaps o 'ubiquitously expresses' SubPropertyOf expresses

(^ The one danger here is that the overlap is between non-cellular components of X and Y).

We can distinguish a whole expression pattern from a regional expression pattern.  The former consists of all cells in an animal that express a specific gene product. The latter consists of all cells in some specific anatomical structure that express a specific gene product.  For example the regional expression pattern of the Notch gene in the adult brain is the the mereological sum of all cells in the adult brain that express Notch.

* 'complete expression pattern of X':  The mereological sum of all cells in an organism (at all stages) that express X.
  * _EquivalentTo_: 'complete expression pattern' that 'EquivalentTo expresses' some X
  * _GCI_: cell and (expresses some X) SubClassOf 'part of' some 'expression pattern of X'
  * _GCI_: 'regional expression pattern' and (ubiquitously_expresses some 'feature X') SubClassOf 'part of' some 'expression pattern of X'
  
Classes following this pattern can be used to aggregate axioms recording the expression of gene X. The second GCI can be used  

* 'expression pattern of X in adult brain': 
  * _EquivalentTo_: 'regional expression pattern' that ('ubiquitously expresses' some X) and ('part of'^^ some 'adult brain')
  * _GCI_: 'regional expression pattern' and ('ubiquitously expresses' some 'feature X') and ('part of' some 'adult brain')   SubClassOf 'part of' some 'expression pattern of X in adult brain'
  * _GCI_: cell and (' ubiquitously expresses' some 'feature X') and ('part of' some 'adult brain') SubClassOf 'part of' some 'expression pattern of X in adult brain'

This second pattern is useful to provide a more precise grouping images of patterns by anatomical region (e.g. one correponding to an image registration template). 

## Mapping from database tag-value annotations

Many databases contain annotations recording the localization of expression.  The semantics of these annotations are typically semi-formal and underspecified.  But they are often very detailed, recording anatomical and subcellular locations, stages and whether protein or transcript was detected and even whether the gene product is expressed throughout or in all subclasses of the specified structure.

It may be foolish to specify a universal mapping from such annotations, but:

It seems reasonable to assume that, in the vast majority of cases, RNA localization to cells corresponds to sites of expression, whereas protein localization does not necessarily correspond.  Annotation with a class term does not necessarily indicate localization to all subclasses of that structure

Annotation:  'RNA product of gene X' :: 'Cell class Y' -> 
OWL:'expression pattern of gene X' SubClassOf has_part some Y

Annotation: 'RNA product of gene X'  :: 'multicellular anatomical structure Y' -> 
OWL:'expression pattern of gene X' SubClassOf overlaps some 'multicellular anatomical structure Y'

Many databases also contain information about stages of expression.  In OWL we can express this with an anonymous class:

Annotation: 'RNA product of gene X' ; 'multicellular anatomical structure Y' ; larval stage -> 
OWL:'expression pattern of gene X' SubClassOf overlaps some ('multicellular anatomical structure Y' that 'exists during' some 'larval stage'

With these patterns. Querying for expression patterns is straightforward and requires no anatomy pre-query.

'expression pattern' that overlaps some X is sufficient

But if users are interested in genes?  How do we get a list of these?  With a graph database, one can run a pattern-match to navigate back over 'ubiquitously expresses' relations to find a transgene.  Results lists can then include both.

The pure OWL approach has a couple of disadvantages:  

* Given that these datasets can get very large, OWL reasoning may not be an optimal approach for querying.   
* OWL axioms can be annotated to record supporting references, but returning these from queries is not straightforward.

An alternative approach is to use a graph database or triple store + graph pattern matching. For example, one can use an OWL query for the  the first step: returning a list of structures that overlap some structure X with the final step relying on graph pattern matching.  This has the advantage that it is easy to return supporting references and other metadata from multiple nodes in the graph match pattern.  An example of this type of representation can be see in this GraphGist: 

![image](https://cloud.githubusercontent.com/assets/112839/19857275/febda88a-9f74-11e6-9fa0-01b1c58b0463.png)

http://portal.graphgist.org/graph_gists/1cead583-7fdf-4f4d-95c8-07b828168b8c

The graph database approach here suffers from the disadvantage that intermediate nodes need to be instantiated to deal with staging.  On the plus side, it is straightforward to use Neo4J infer intermediate stages of expression when a stage range is given, as long as the stage ontology records temporal ordering, something that is surprisingly hard in OWL.

The two approaches can potentially be combined using a pre-reasoned triple store such as RDFox.













	


	
	

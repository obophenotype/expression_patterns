## Aims

Expression data can take many forms:  Annotations with ontology terms; expression pattern images in 2D or 3D, registered to a reference standard or unregistered; Image of single labelled cells or clones of cells that make up some part of an expression pattern; RNA seq data for identified tissues, groups of cells or single cells.

The aim of this site is to document semantic patterns that can be used to relate annotations to images, samples or drawn from papers so that the data from all of them can be integrated into a queryable whole.

Example use cases:

* Querying for genes expressed in some specified structure
* Finding images of the expression pattern of some specified gene or transgene
* Finding images of expression patterns
* Finding images of expression pattern fragments.

## Defining expression patterns

We distinguish between an expression pattern and a localization pattern:  

An expression pattern consists of the mereological sum of all cells expressing some specific gene product in an organism or in some specified region of an organism.  A localization pattern is the pattern of localization of a gene product at the cellular or or subcellular level. It may include non-cellular structures and structures distant from the site of expression.  

We define 'expresses': X expresses Y iff:  Y is a [gene](http://www.ebi.ac.uk/ols/ontologies/so/terms?iri=http%3A%2F%2Fpurl.obolibrary.org%2Fobo%2FSO_0000704) X is a cell or a cellular anatomical structure.  (['gene expression'](http://www.ebi.ac.uk/ols/ontologies/go/terms?iri=http%3A%2F%2Fpurl.obolibrary.org%2Fobo%2FGO_0010467) and 'has input' some Y) 'occurs' in all (cell that 'part of' Y).

i.e. at the cellular level, and expression pattern is binary. 

We can distinguish a whole expression pattern from a regional expression pattern.  The former consists of all cells in an animal that express a specific gene product. The latter consists of all cells in some specific anatomical structure that express a specific gene product.  For example the regional expression pattern of the Notch gene in the adult brain is the the mereological sum of all cells in the adult brain that express Notch.

'complete expression pattern of X:  The mereological sum of all cells in an organism (at all stages) that express X.
SubClassOf: 'complete expression pattern' that expresses some X
GCI: cell and (expresses some X) SubClassOf 'part of' some 'expression pattern of X'
GCI: 'regional expression pattern' and (expresses some 'feature X') SubClassOf 'part of' some 'expression pattern of X'

'expression pattern of X in adult brain': 
EquivalentTo: 'regional expression pattern' that (expresses some X) and ('part of'^^ some 'adult brain')
GCI: 'regional expression pattern' and (expresses some 'feature X') and ('part of' some 'adult brain') SubClassOf 'part of' some 'expression pattern of X in adult brain'
GCI: cell and (expresses some 'feature X') and ('part of' some 'adult brain') SubClassOf 'part of' some 'expression pattern of X in adult brain'


## Mapping from database tag-value annotations

Many databases contain annotations recording the localization of expression.  The semantics of these annotations are typically semi-formal and underspecified.  But they are often very detailed, recording anatomical and subcellular locations, stages and whether protein or transcript was detected and even whether the gene product is expressed throughout or in all subclasses of the specified structure.

It may be foolish to specify a universal mapping from such annotations, but:

It seems reasonable to assume that RNA localization to cells corresponds to sites of expression, whereas protein localization does not necessarily correspond.  Annotation with a class term does not necessarily indicate localization to all subclasses of that structure, but we can record 

Annotation:  'RNA product of gene X' - 'Cell class Y' -> OWL:
'expression pattern of gene X' SubClassOf has_part some Y

Annotation: 'RNA product of gene X'  - 'multicellular anatomical structure Y' -> OWL":
'expression pattern of gene X' SubClassOf overlaps some 'multicellular anatomical structure Y'

*Querying for genes expressed in structure X:*

'expression pattern' that overlaps some X

*Querying for genes expressed in cell Y:*

'expression pattern' that has_part some Y









	


	
	

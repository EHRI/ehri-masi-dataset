EHRI Multilingual Subject Indexing Test Dataset
===============================================

Overview
--------

This dataset consists of texts, derived from descriptions of Holocaust-related archival material, each of which is
associated with one or more subject terms from the [EHRI Terms](https://portal.ehri-project.eu/vocabularies/ehri_terms) 
controlled vocabulary. 

This dataset is described more thoroughly in the following paper, and is published to support reproduction:

    Multilingual Automated Subject Indexing: a comparative study of LLMs vs alternative approaches in the context of the EHRI project

Authors
~~~~~~~

- Maria Dermentzi, Orcid: 0000-0001-8159-7600
- Mike Bryant, Orcid: 0000-0003-0765-7390
- Fabio Rovigo, Orcid: 0000-0001-5760-3185
- Herminio García-González, Orcid: 0000-0001-5590-4857

License
~~~~~~~

See accompanying file: LICENSE.txt

Data collection
~~~~~~~~~~~~~~~

The data has been exported from the [EHRI Portal](https://portal.ehri-project.eu/)
on 2023-10-11.

Structure
~~~~~~~~~

The dataset is split into a training and test portion, comprising 25,732 and 10,860
descriptions respectively. There is an additional (smaller) test set for qualitative
evaluation purposes (named `eval`) consisting of 167 items.

Data processing
~~~~~~~~~~~~~~~

The processing applied to this dataset and the stratification techniques used are
described in the referenced paper.

Format
~~~~~~

The data is structured in Annif [Full-text document corpus
format](https://github.com/NatLibFi/Annif/wiki/Document-corpus-formats), 
where the basename of each file is the ID of an item on the EHRI portal. 
For example, the text for a description is containing in `some-id.txt` 
and the associated subject terms in `some-id.tsv`.

Reproducibility information
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Python:
  3.10.12
Annif:
  1.1.0.dev0

To reproduce the results of the paper, follow these steps:

    # Note: *do this in a virtual environment*
    # This assumes you're using virtualenvwrapper
    mkvirtualenv ehri_masi
    workon ehri_masi

    # Checkout and install the modified version of Annif.
    git clone https://github.com/EHRI/Annif.git EHRI_MASI
    cd EHRI_MASI
    git checkout ehri_masi
    pip install -e .[nn,fasttext,spacy,omikuji]

    # Clone the dataset to the data directory
    git clone https://github.com/EHRI/ehri-masi-data.git data

    # Load the test vocabulary with the name used in the project.cfg file
    annif load-vocab ehri_sm data/ehri_sm.ttl

    # Train the models
    for model in tfidf-ehri mllm-ehri fasttext-ehri omikuji-parabel-ehri nn-ehri; do
      echo "Training $model..."
      annif train $model data/train
    done

    # Evaluate the models
    for model in tfidf-ehri mllm-ehri fasttext-ehri omikuji-parabel-ehri nn-ehri; do
      echo "Evaluating $model..."
      annif eval $model data/test
    done

Version history
~~~~~~~~~~~~~~~

v1.0.0 (2025-01-13): Initial release for paper submission.

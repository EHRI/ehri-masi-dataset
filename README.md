# EHRI Multilingual Subject Indexing Test Dataset

## Overview

This dataset consists of texts, derived from descriptions of Holocaust-related archival material, each of which is
associated with one or more subject terms from the [EHRI Terms](https://portal.ehri-project.eu/vocabularies/ehri_terms)
controlled vocabulary.

This dataset is described more thoroughly in the following paper, and is published to support reproduction:

    Multilingual Automated Subject Indexing: a comparative study of LLMs vs alternative approaches in the context of the EHRI project

## Authors

- Maria Dermentzi, Orcid: 0000-0001-8159-7600
- Mike Bryant, Orcid: 0000-0003-0765-7390
- Fabio Rovigo, Orcid: 0000-0001-5760-3185
- Herminio García-González, Orcid: 0000-0001-5590-4857

## License

See accompanying file: LICENSE.txt

## Data collection

The data has been exported from the [EHRI Portal](https://portal.ehri-project.eu/)
on 2023-10-11.

## Structure

The dataset is split into a training and test portion, comprising 25,732 and 10,860
descriptions respectively. There is an additional (smaller) test set for evaluation
purposes (named `eval`) consisting of 167 items.

## Data processing

The processing applied to this dataset and the stratification techniques used are
described in the referenced paper.

## Format

The data is structured in Annif [Full-text document corpus
format](https://github.com/NatLibFi/Annif/wiki/Document-corpus-formats),
where the basename of each file is the ID of an item on the EHRI portal.
For example, the text for a description is containing in `some-id.txt`
and the associated subject terms in `some-id.tsv`.

## Reproducibility

To reproduce the results of the paper, follow these steps:

    # Install GIT LFS, method here assumes Debian-based Linux 
    sudo apt install git-lfs
    git lfs install

    # Note: *do this in a virtual environment*
    # This assumes you're using virtualenvwrapper
    mkvirtualenv ehri_masi
    workon ehri_masi

    # Checkout and install the modified version of Annif.
    git clone https://github.com/EHRI/Annif.git ehri_masi
    cd ehri_masi

    # Checkout the branch with the modifications for this paper
    git checkout ehri_masi

    # Install python dependencies
    pip install -r requirements-ehri.txt
    pip install -e .[nn,fasttext,spacy,omikuji]

    # Clone the dataset to the data directory
    git clone https://github.com/EHRI/ehri-masi-data.git data

    # Activate the example `projects.cfg`
    cp projects.cfg.ehri projects.cfg

    # Load the test vocabulary with the name used in the project.cfg file
    annif load-vocab ehri_sm data/ehri_sm.ttl

    # Clone the model for the EHRI fine-tuned BERT-based model to the `models` directory
    # NB: this may take some time...
    git clone https://huggingface.co/mdermentzi/finetuned-bert-base-multilingual-cased-ehri-terms models/finetuned-bert-base-multilingual-cased-ehri-terms

    # Train the Annif models
    for model in tfidf-ehri mllm-ehri fasttext-ehri omikuji-parabel-ehri nn-ehri; do
      echo "Training $model..."
      annif train $model data/train
    done

    # Evaluate the Annif models
    for model in tfidf-ehri mllm-ehri fasttext-ehri omikuji-parabel-ehri nn-ehri; do
      echo "Evaluating $model..."
      annif eval $model data/eval
    done

    # Evaluate the EHRI fine-tuned model
    annif eval bertft-ehri data/eval

    # Evaluate the MDeBERTa based zero-shot model. This will be extremely slow
    # unless you have a good CUDA-supporting graphics card...
    annif eval mdeberta-ehri data/eval

## Version history

v1.0.0 (2025-01-15): Initial release for paper submission.

**D3NER Program**
================

D3NER, version 1.0, is a program that was developed by AiDante team.
The program has 3 main purposes:
- Recognizing disease and chemical entities in text documents
- Evaluating pre-trained models with test dataset
- Training new models with given corpora that follow the BioCreative V format

----------

Contacts
------------

If you have any questions or problems, please e-mail **hai.dang@vnu.edu.vn**

Installation
---------------

This program was developed using Python version 3.5 and was tested on Linux and Windows system.
We recommend using Anaconda 4.2 for installing **Python 3.5** as well as **numpy**, although you can install them by other means.

Other requirements:

1. **Tensorflow** GPU 1.4.0:

> pip install tensorflow-gpu==1.4.0

If GPU is not available, tensorflow CPU can be used instead:
> pip install tensorflow==1.4.0

2. **Keras** 2.1.1:
> pip install Keras==2.1.1

3. **Sklearn** 0.19.1:
> conda install scikit-learn==0.19.1

4. **NLTK** 3.2.5:
> conda install nltk==3.2.5

5. **SpaCy** 1.9:
> conda install spacy==1.9

and spaCy model:
> python -m spacy download en_core_web_md

6. **Ab3P**:

  Running the D3NER program, re-training models or evaluating the pre-trained models on the included corpora does not require Ab3P to be installed because its result has been prepared in the "ab3p_tfidf.pickle" file within each corpus.
On the other hand, if you wish to train a new model with different corpora, installation instruction, and usage of Ab3P is available at https://github.com/ncbi-nlp/Ab3P

Usage
---------

#### **Recognize disease and chemical entities in text documents**
Command:

    python main.pyc -h
    usage: main.pyc [-h] model dataset input_file output_file

    D3NER Program: Recognize disease or chemical entities in text documents.

    positional arguments:
      model        the name of the model being used, i.e: d3ner_cdr
      dataset      the name of the dataset that the model was trained on, i.e: cdr
      input_file   path to the input file, i.e: data/cdr/cdr_test.txt
      output_file  path to the output file, i.e: output.txt

    optional arguments:
      -h, --help   show this help message and exit

> **Note:**
> - "model" is the direct child folder within the "pre_trained_models/" folder
> - "dataset" is the direct child folder within the "data/" folder
> - "input_file" is the path to the file that contains one or more text document(s) that each follows the below format: 
>```
><id>|t|<text>
><id>|a|<text>
>```
> which `<id>` and `<text>` can be replaced with appropriate information. Noting that this format is similar to the BioCreative V format but the annotation part is unnecessary and it does not matter whether included or not.

Example: Running the CDR test file with the model trained on CDR corpus and write the result to output.txt file

    python main.pyc d3ner_cdr cdr data/cdr/cdr_test.txt output.txt

#### **Evaluating pre-trained models**

> **Important Note:** The pre-trained models we included with the program were trained on Windows system. For that reason, evaluating those models on different OSs might give very different results.
> Evaluating on Windows will give these results:
> - For the BioCreative V CDR dataset:
>```
>                P       R       F1
>        Dis:    85.14   85.35   85.25
>        Chem:   93.47   93.04   93.25
>```
> - For the NCBI dataset:
>```
>                P       R       F1
>        Dis:    85.74   85.83   85.79
>```

Command:

    python -m train.evaluate -h
    usage: evaluate.pyc [-h] [-cf] model dataset test_set

    Evaluate trained model.

    positional arguments:
      model                 the name of the model being used, i.e: d3ner_cdr
      dataset               the name of the dataset that the model will be trained on, i.e: cdr
      test_set              path to the test dataset, i.e: data/cdr/cdr_test.txt

    optional arguments:
      -h, --help            show this help message and exit
      -cf, --confusion_matrix
                            report confusion matrix

> **Note:**
> - "model" is the direct child folder within the "pre_trained_models/" folder
> - "dataset" is the direct child folder within the "data/" folder

Example: Evaluating the model trained on CDR corpus using CDR test data and also report the confusion matrix

    python -m train.evaluate d3ner_cdr cdr data/cdr/cdr_test.txt -cf

#### **Training new models**
[**Step 0** - Download the pre-trained word embedding model]

Any pre-trained word embedding model can be used but it must be the same format as our provided word embedding model, which we highly recommend to be used. You can download the model file named "**wikipedia-pubmed-and-PMC-w2v.bin**" at this link: http://evexdb.org/pmresources/vec-space-models/

After the file was downloaded, place it within the "**pre_trained_models/**" folder.

[**Step 1** - Prepare the data]

All the data must be placed inside the `"data/<dataset_name>/"` folder, with `<dataset_name>` is replaced appropriately. The corpus that will be used for training must follow the BioCreative V format and be divided into three dataset: **train**, **development** and **test**.

The data will be prepared when running the follow command:

    python -m train.build_data <dataset> <train_set> <dev_set> <test_set> <word_embedding> <ab3p_path>

> **Note:** Building data could take a while so be patient!

For more details, run:

    python -m train.build_data -h
    usage: build_data.pyc [-h] dataset train_set dev_set test_set word_embedding ab3p

        Build necessary data for model training and evaluating.

        positional arguments:
          dataset         the name of the dataset that the model will be trained on, i.e: cdr
          train_set       path to the training dataset, i.e: data/cdr/cdr_train.txt
          dev_set         path to the development dataset, i.e: data/cdr/cdr_dev.txt
          test_set        path to the test dataset, i.e: data/cdr/cdr_test.txt
          word_embedding  path to the word embedding pre-trained model, i.e: pre_trained_models/wikipedia-pubmed-and-PMC-w2v.bin
          ab3p            path to the Ab3P program.

        optional arguments:
          -h, --help      show this help message and exit

[**Step 2** - Training]

Command:

    python -m train.run -h
    usage: run.pyc [-h] [-es | -e EPOCH] [-v] model dataset train_set dev_set

    Train new model.

    positional arguments:
      model                 the name of the model, i.e: d3ner_cdr
      dataset               the name of the dataset that the model will be trained on, i.e: cdr
      train_set             path to the training dataset, i.e: data/cdr/cdr_train.txt
      dev_set               path to the development dataset, i.e: data/cdr/cdr_dev.txt

    optional arguments:
      -h, --help            show this help message and exit
      -es, --early_stopping
                            use early stopping
      -e EPOCH, --epoch EPOCH
                            number of epochs to train
      -v, --verbose         print training process

Example: Training new model on CDR corpus with early stopping option

    python -m train.run d3ner_cdr cdr data/cdr/cdr_train.txt data/cdr/cdr_dev.txt -es

  [1]: Curated chemical and disease vocabulary data were retrieved from the Comparative Toxicogenomics Database (CTD), MDI Biological Laboratory, Salisbury Cove, Maine, and NC State University, Raleigh, North Carolina. World Wide Web (URL: http://ctdbase.org/). October, 2017

  [2]: Mikolov,T. et al. (2013) Distributed representations of words and phrases and their compositionality. Advances in neural information processing systems, pp. 3111-3119.
  
  [3]: Sohn,S. et al. (2008) Abbreviation definition identification based on automatic precision estimates. BMC bioinformatics, 9(1), pp. 402.

Most of the code is based on [TypeSQL](https://github.com/taoyds/typesql) and [SQLNet](https://github.com/xiaojunxu/SQLNet). 
Please cite both TypeSQL and SQLNet if you use this code.

## TypeSQL with BERT

BERT byte-pair encoded tokens had to be rejoined into WikiSQL tokens as otherwise the SQL generation task will not work (due to BERT tokenizer's sub-word splitting). Go to branch `BERT_TypeSQL` to see implementation and run Type SQL with BERT instead of GloVe embeddings.

## TypeSQL with POS

POS tags of natural language questions were tagged using NLTK's POS tagger. 
Go to branch `POS_TypeSQL` to see implementation and run TypeSQL with POS embeddings as additional features. 

## TypeSQL

Source code accompanying TypeSQL's NAACL 2018 paper:[TypeSQL: Knowledge-based Type-Aware Neural Text-to-SQL Generation
](https://arxiv.org/abs/1804.09769)

#### Environment Setup

1. The code uses Python 3.7, [Pytorch 1.1.0](https://pytorch.org/previous-versions/) and [Pytorch-Transformers](https://github.com/huggingface/pytorch-transformers)..
2. Install Python dependency: `pip install -r requirements.txt`
3. Install Pytorch: `pip install pytorch`.
4. Install Pytorch Transformers: `pip install pytorch-transformers`. Go to their repo for more information about requirements and dependencies.

#### Download Data and Embeddings

1. Download the zip data file at the [Google Drive](https://drive.google.com/file/d/1CGIRCjwf2bgmWl3UyjY1yJpP4nU---Q0/view?usp=sharing), and put it in the root dir.
2. Download the pretrained [Glove](https://nlp.stanford.edu/data/wordvecs/glove.42B.300d.zip) and the [paraphrase embedding](https://drive.google.com/file/d/1iWTowxEG1-KZyq-fHP6cb6dNqMh4eHiN/view?usp=sharing) `para-nmt-50m/data/paragram_sl999_czeng.txt`. Put the unziped glove and para-nmt-50m folders in the root dir. (Neither do we use BERT embeddings to predict aggregate values in the SELECT clause nor to compute Type embeddings - as in both cases there is no context to disentangle).
3. Use the pre-trained BERT model ('uncased') from [Pytorch-Transformers](https://github.com/huggingface/pytorch-transformers).

#### Train Models

1. To use knowledge graph types:
```
  mkdir saved_model_kg
  python train.py --sd saved_model_kg
```

  1.1 To use BERT embeddings:

  ```
    mkdir saved_model_kg
    python train.py --sd saved_model_kg --BERT True
  ```

  1.2 To concatenate BERT with Type embeddings:

  ```
    mkdir saved_model_kg
    python train.py --sd saved_model_kg --BERT True --types True (false, if you want to use BERT embeddings only - no concatenation)
  ```


2. To use DB content types:
```
   mkdir saved_model_con
   python train.py --sd saved_model_con --db_content 1
```

  2.1 To use BERT embeddings:

  ```
    mkdir saved_model_con
    python train.py --sd saved_model_con --db_content 1 --BERT True
  ```

  2.2 To concatenate BERT with Type embeddings:

  ```
    mkdir saved_model_kg
    python train.py --sd saved_model_con --db_content 1 --BERT True --types True (false, if you want to use BERT embeddings only - no concatenation)
  ```

#### Test Models

1. Test Model with knowledge graph types:
```
python test.py --sd saved_model_kg
```
2. Test Model with knowledge graph types:
```
python test.py --sd saved_model_con --db_content 1
```

#### Get Data Types

1. Get a Google Knowledge Graph Search API Key by following the [link](https://developers.google.com/knowledge-graph/)
2. Search knowledge graph to get entities:
```
python get_kg_entities.py [Google freebase API Key] [input json file] [output json file]
```
3. Use detected knowledge graph entites and DB content to group questions and create type attributes in data files:
```
python data_process_test.py --tok [output json file generated at step 2] --table TABLE_FILE --out OUTPUT_FILE [--data_dir DATA_DIRECTORY] [--out_dir OUTPUT_DIRECTORY]

python data_process_train_dev.py --tok [output json file generated at step 2] --table TABLE_FILE --out OUTPUT_FILE [--data_dir DATA_DIRECTORY] [--out_dir OUTPUT_DIRECTORY]
```

#### Acknowledgement

The implementation is based on [TypeSQL](https://github.com/taoyds/typesql) and [SQLNet](https://github.com/xiaojunxu/SQLNet). Please cite it too if you use this code.

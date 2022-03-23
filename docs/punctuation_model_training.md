# Training punctuation model


[Repo](https://github.com/Open-Speech-EkStep/punctuation-ITN/tree/wandb-v1)

In this we finetune a [IndicBERT](https://indicnlp.ai4bharat.org/indic-bert/) model (multilingual ALBERT model trained on large-scale corpora, covering 12 major Indian languages: Assamese, Bengali, English, Gujarati, Hindi, Kannada, Malayalam, Marathi, Oriya, Punjabi, Tamil, Telugu.) 

Code is linked with Wandb to monitor our training in real-time. And all input data, intermediate reults and resulting checkpoint are picked and stored in Google Cloud Platform (GCP) bucket

## 1. Data Preparation

make changes in [config.yaml](https://github.com/Open-Speech-EkStep/punctuation-ITN/blob/wandb-v1/sequence_labelling/config.yaml) based on your data. and run [make_data.py](https://github.com/Open-Speech-EkStep/punctuation-ITN/blob/wandb-v1/sequence_labelling/make_data.py) to generate train, test and valid csvs.

Cleaning steps
1. normalize text corpus
2. tokenize text corpus
3. replace foreign characters not punctuation and numerals with space



For punctuation symbol we have taken only [".", ",", "?"] these 3 symbols, which can be changed in [process_raw_text.py](https://github.com/Open-Speech-EkStep/punctuation-ITN/blob/wandb-v1/sequence_labelling/prep_scripts/process_raw_text.py) and [prepare_csv.py](https://github.com/Open-Speech-EkStep/punctuation-ITN/blob/wandb-v1/sequence_labelling/prep_scripts/prepare_csv.py) 

## 2. Start Training 

format of input csvs file for training

> sentence_index,sentence,label

where label maps what is the next punctuation symbol for the corresponding word in sentence.



To start training change training parameters from [training_params.py](https://github.com/Open-Speech-EkStep/punctuation-ITN/blob/wandb-v1/sequence_labelling/token_classification/training_params.py) and run  [train.py](https://github.com/Open-Speech-EkStep/punctuation-ITN/blob/wandb-v1/sequence_labelling/token_classification/train.py)


## 3. Inference

To infer sentences check this file [inference.py](https://github.com/Open-Speech-EkStep/punctuation-ITN/blob/wandb-v1/sequence_labelling/token_classification/inference.py)

Also there is a spearate repository to try already built punctation model in indic langauges [indic-punct](https://github.com/Open-Speech-EkStep/indic-punct#punctuation)

## 4 To use our models


```bash
git clone https://github.com/Open-Speech-EkStep/indic-punct.git
cd indic-punct
bash install.sh
python setup.py bdist_wheel
pip install -e .
```

Currently (v 2.0.6) we are supporting the following languages:
Punctuation:

- Hindi ('hi')
- English ('en')
- Gujarati ('gu')
- Telugu ('te')
- Marathi ('mr')
- Kannada ('kn')
- Punjabi ('pa')
- Tamil ('ta')
- Bengali ('bn')
- Odia ('or')
- Malayalam ('ml')
- Assamese ('as')


```python
from punctuate.punctuate_text import Punctuation
hindi = Punctuation('hi') #loads model in memory
hindi.punctuate_text(["इस श्रेणी में केवल निम्नलिखित उपश्रेणी है", "मेहुल को भारत को सौंप दिया जाए"])
```
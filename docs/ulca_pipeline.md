# ULCA Pipeline

<!-- TABLE OF CONTENTS -->
## Table of Contents

- [ULCA Pipeline](#ulca-pipeline)
  - [Table of Contents](#table-of-contents)
  - [About](#about)
  - [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [ULCA Dataset Generation](#ulca-dataset-generation)
    - [Unlabeled Dataset](#unlabeled-dataset)
    - [Labeled Dataset](#labeled-dataset)
    - [Unlabeled External Dataset](#unlabeled-external-dataset)
    - [Labeled External Dataset](#labeled-external-dataset)
  - [Domains of Data](#domains-of-data)
  - [Contributing](#contributing)
  - [License](#license)
  - [Git Repository](#git-repository)
  - [Contact](#contact)

<!-- ABOUT-->
## About

ULCA Pipeline is built to create the audio data set that can be contributed to [UNIVERSAL LANGUAGE CONTRIBUTION API (ULCA)](https://bhashini.gov.in/ulca). The aim is to allow easy, quick and fast ULCA format dataset generation without doing manual work.

<!-- GETTING STARTED -->
## Getting Started

ULCA Pipeline works primarily in continuation of Intelligent Data pipeline on same environment. To generate ULCA format zip data, few Airflow variable needs to be configured and corresponding DAGs to be triggered. The input to ULCA pipeline is processed data generated through Intelligent Data pipeline. If we want to input some other data then that need to processed to a proper format which we will discuss in later sections. 


<!-- Prerequisites-->
## Prerequisites

- Well configured **Composer and Airflow setup** similar to Intelligent Data Pipeline.

- Data should be stored on Bucket.


<!-- ULCA_DATASET_GENERATION-->  
## ULCA Dataset Generation

ULCA datasets can be categorised into 4 types:

- [Unlabeled Dataset](#unlabeled-dataset)
- [Labeled Dataset](#labeled-dataset)
- [Unlabeled External Dataset](#unlabeled-external-dataset)
- [Labeled External Dataset](#labeled-external-dataset)


### Unlabeled Dataset

Dataset that only contains audio files but not corresponding text files. And generated through Intelligent Data Pipeline.

#### Steps to Run:

- We have to configure **ulca_dataset_config** in airflow variables where we need to add our source(s) name which we want to process and configure following parameters:
    - **language**: Source language name.
    - **source_path**: path of the data
    - **export_count**: Count of audio files that you want in one ULCA format zip file. Ingesting zip file with more than 10,000 files cause an error.
    - **is_transcribed**: This should be **False** in case of unlabeled dataset.
    - **include_rejected**: This should be **False** if you don't want to include rejected audio files by Intelligent data pipeline while True if you want to  include rejected audio files too.
    - **labelled**: This should be **False** in case of unlabeled dataset.
    - **publish_path**: path where you want to store ULCA format zip files.
    - **datasetType**: This should be **asr-unlabeled-corpus** in case of unlabeled dataset.
    - **sourceLanguage**: Source language ISO Code.
    - **collectionSource**: Source name.
    - **domain**: Domain of data. Domain should be present in this [domain list](#domains-of-data). If not, then domain should be **general**.
    - **license**: Type of license.
    - **name**: Name of organization or contributor.
    - **oauthId**: Email address.
    - **provider**: Name of organization or contributor.
    - **format**: Format of the audio file.
    - **methodType**: Method type of SNR used to prepare data.
    - **snrTool**: Name of SNR tool used to prepare data.
    - **snrToolVersion**: Version number of SNR tool used to prepare data.

    ex:-

```json
{
  "source1_name":{
    "language":"<LANGUAGE NAME>",
    "source_path":"<PATH WHERE DATA IS STORED>",
    "export_count": 5000,
    "is_transcribed":"False",
    "include_rejected":"False",
    "labelled":"False",
    "publish_path":"<OUTPUT PATH>",
    "params":{
      "datasetType":"asr-unlabeled-corpus",
      "languages":{
        "sourceLanguage":"<LANGUAGE CODE>"
      },
      "collectionSource":[
        "source1_name"
      ],
     "domain":[
        "<DOMAIN>"
     ],
     "license":"<LICENSE_TYPE>",
     "submitter":{
     "name":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>",
     "oauthId":{
        "oauthId":"<EMAIL ADDRESS>",
        "provider":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>"
     },
     "team":[
          {
             "name":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>",
             "oauthId":{
                "oauthId":"<EMAIL ADDRESS>",
                "provider":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>"
             }
          }
       ]
    },
    "format":"wav",
    "snr":{
       "methodType":"WadaSnr",
       "methodDetails":{
          "snrTool":"WADASNR",
          "snrToolVersion":"1.0.1"
        }
      }
    }
  },
  "source2_name":{
    "language":"<LANGUAGE NAME>",
    "source_path":"<PATH WHERE DATA IS STORED>",
    "export_count": 20000,
    "is_transcribed":"False",
    "include_rejected":"False",
    "labelled":"False",
    "publish_path":"<OUTPUT PATH>",
    "params":{
      "datasetType":"asr-unlabeled-corpus",
      "languages":{
        "sourceLanguage":"<LANGUAGE CODE>"
      },
      "collectionSource":[
        "source2_name"
      ],
     "domain":[
        "<DOMAIN>"
     ],
     "license":"<LICENSE_TYPE>",
     "submitter":{
     "name":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>",
     "oauthId":{
        "oauthId":"<EMAIL ADDRESS>",
        "provider":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>"
     },
     "team":[
          {
             "name":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>",
             "oauthId":{
                "oauthId":"<EMAIL ADDRESS>",
                "provider":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>"
             }
          }
       ]
    },
    "format":"wav",
    "snr":{
       "methodType":"WadaSnr",
       "methodDetails":{
          "snrTool":"WADASNR",
          "snrToolVersion":"1.0.1"
        }
      }
    }
  }
}
```

- That will create a DAG named **ulca_dataset_pipeline**. Now, we can trigger that DAG and that will process given source(s) and upload ULCA format dataset zip file to **publish_path** that we mentioned in the Airflow variable. And, database will be updated with the metadata.


### Labeled Dataset

Dataset that contains both audio files and corresponding text files. And generated through Intelligent Data Pipeline.

#### Steps to Run:

- We have to configure **ulca_dataset_config** in airflow variables where we need to add our source(s) name which we want to process and configure certain parameters alike Unlabeled dataset except following parameters:

    - **is_transcribed**: This should be **True** in case of labeled dataset.
    - **labelled**: This should be **True** in case of labeled dataset.
    - **datasetType**: This should be **asr-corpus** in case of unlabeled dataset.
    - **collectionDescription**: This should be **machine-generated-transcript** if transcriptions are generated using ASR model.
    - **asrModel**: Name of Automatic Speech Recognition (ASR) model used to generate transcriptions.
    - **wer**: Word Error Rate of ASR Model used to generate transcriptions.
 
    ex:-

```json
{
  "source_name":{
    "language":"<LANGUAGE NAME>",
    "source_path":"<PATH WHERE DATA IS STORED>",
    "export_count": 5000,
    "is_transcribed":"True",
    "include_rejected":"False",
    "labelled":"True",
    "publish_path":"<OUTPUT PATH>",
    "params":{
      "datasetType":"asr-corpus",
      "languages":{
        "sourceLanguage":"<LANGUAGE CODE>"
      },
      "collectionSource":[
        "source1_name"
      ],
     "domain":[
        "<DOMAIN>"
     ],
     "collectionMethod":{
        "collectionDescription":[
          "machine-generated-transcript"
        ],
        "collectionDetails":{
          "asrModel":"<ASR_MODEL_NAME>",
          "evaluationMethod":{
            "wer":<WER_SCORE_OF_ASR_MODEL>
          }
        }
     }, 
     "license":"<LICENSE_TYPE>",
     "submitter":{
     "name":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>",
     "oauthId":{
        "oauthId":"<EMAIL ADDRESS>",
        "provider":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>"
     },
     "team":[
          {
             "name":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>",
             "oauthId":{
                "oauthId":"<EMAIL ADDRESS>",
                "provider":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>"
             }
          }
       ]
    },
    "format":"wav",
    "snr":{
       "methodType":"WadaSnr",
       "methodDetails":{
          "snrTool":"WADASNR",
          "snrToolVersion":"1.0.1"
        }
      }
    }
  }
}
```

- That will create a DAG named **ulca_dataset_pipeline**. Now, we can trigger that DAG and that will process given source(s) and upload ULCA format dataset zip file to **publish_path** that we mentioned in the Airflow variable. And, database will be updated with the metadata.


### Unlabeled External Dataset

Dataset that is not generated through Intelligent Data Pipeline and only contains audio files but not corresponding text files.

#### Steps to Run:

- We have to configure **ulca_dataset_config** in airflow variables where we need to add our source(s) name which we want to process and configure certain parameters alike Unlabeled dataset except following parameters:

    - **is_external**: This should be **True** in case of unlabeled external dataset.
    - **is_transcribed**: This should be **False** in case of unlabeled dataset.
    - **labelled**: This should be **False** in case of unlabeled dataset.
    - **datasetType**: This should be **asr-unlabeled-corpus** in case of unlabeled dataset.

  ex:-

```json
{
   "source_name":{
      "language":"<LANGUAGE NAME>",
      "source_path":"<PATH WHERE DATA IS STORED>",
      "export_count": 1000,
      "is_transcribed":"False",
      "include_rejected":"False",
      "labelled":"False",
      "is_external":"True",
      "publish_path":"<OUTPUT PATH>",
      "params":{
        "datasetType":"asr-unlabeled-corpus",
        "languages":{
          "sourceLanguage":"<LANGUAGE CODE>"
        },
        "collectionSource":[
          "<source_name>", "external"
        ],
        "domain":[
          "<DOMAIN>"
        ],
        "license":"<LICENSE_TYPE>",
        "submitter":{
        "name":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>",
        "oauthId":{
          "oauthId":"<EMAIL ADDRESS>",
          "provider":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>"
        },
        "team":[
          {
            "name":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>",
            "oauthId":{
            "oauthId":"<EMAIL ADDRESS>",
            "provider":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>"
            }
          }
        ]
      },
      "format":"wav"
    }
  }
}
```

- That will create a DAG named **ulca_dataset_pipeline**. Now, we can trigger that DAG and that will process given source(s) and upload ULCA format dataset zip file to **publish_path** that we mentioned in the Airflow variable. And, database will be updated with the metadata.


### Labeled External Dataset

Dataset that is not generated through Intelligent Data Pipeline and contains both audio files and corresponding text files.

#### Steps to Run:

- We have to configure **ulca_dataset_config** in airflow variables where we need to add our source(s) name which we want to process and configure certain parameters alike labeled dataset except following parameters:

    - **is_external**: This should be **True** in case of unlabeled external dataset.
    - **is_transcribed**: This should be **True** in case of labeled dataset.
    - **labelled**: This should be **True** in case of labeled dataset.
    - **datasetType**: This should be **asr-corpus** in case of labeled dataset.

  ex:-

```json
{
   "source_name":{
      "language":"<LANGUAGE NAME>",
      "source_path":"<PATH WHERE DATA IS STORED>",
      "export_count": 1000,
      "is_transcribed":"True",
      "include_rejected":"False",
      "labelled":"True",
      "is_external":"True",
      "publish_path":"<OUTPUT PATH>",
      "params":{
        "datasetType":"asr-corpus",
        "languages":{
          "sourceLanguage":"<LANGUAGE CODE>"
        },
        "collectionSource":[
          "<source_name>", "external"
        ],
        "domain":[
          "<DOMAIN>"
        ],
        "license":"<LICENSE_TYPE>",
        "submitter":{
        "name":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>",
        "oauthId":{
          "oauthId":"<EMAIL ADDRESS>",
          "provider":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>"
        },
        "team":[
          {
            "name":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>",
            "oauthId":{
            "oauthId":"<EMAIL ADDRESS>",
            "provider":"<ORGANIZATION_NAME/CONTRIBUTOR_NAME>"
            }
          }
        ]
      },
      "format":"wav"
    }
  }
}
```

- That will create a DAG named **ulca_dataset_pipeline**. Now, we can trigger that DAG and that will process given source(s) and upload ULCA format dataset zip file to **publish_path** that we mentioned in the Airflow variable. And, database will be updated with the metadata.

<!-- DOMAINS OF DATA-->
## Domains of Data

ULCA has support for tagging dataset with domain. Following are the supported domains: 

- general
- news
- education
- legal
- government-press-release
- healthcare
- agriculture
- automobile
- tourism
- financial
- movies
- subtitles
- sports
- technology
- lifestyle
- entertainment
- parliamentary
- art-and-culture
- economy
- history
- philosophy
- religion
- national-security-and-defence
- literature
- geography


<!-- CONTRIBUTING -->
## Contributing

Contributions are what make the open source community such an amazing place to be learn, inspire, and create. Any contributions you make are **greatly appreciated**.

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

We follow [conventional commits](https://www.conventionalcommits.org/en/v1.0.0/)

<!-- LICENSE -->
## License

Distributed under the [MIT] License. See [LICENSE](https://github.com/Open-Speech-EkStep/audio-to-speech-pipeline/blob/master/LICENSE) for more information.

## Git Repository

[https://github.com/Open-Speech-EkStep/audio-to-speech-pipeline](https://github.com/Open-Speech-EkStep/audio-to-speech-pipeline)

## Contact

Connect with community on [Gitter](https://gitter.im/Vakyansh/community?utm_source=share-link&utm_medium=link&utm_campaign=share-link)

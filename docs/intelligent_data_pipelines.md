# Intelligent Data Pipeline

<!-- TABLE OF CONTENTS -->
## Table of Contents

* [About the Project](#about-the-project)
* [Architecture](#architecture)
* [Built With](#built-with)
* [Getting Started](#getting-started)
* [Run on GCP (with composer)](#setup)
* [Installation](#installation)
* [Contributing](#contributing)
* [License](#license)
* [Contact](#contact)
* [Acknowledgements](#acknowledgements)



<!-- ABOUT THE PROJECT -->
## About The Project

This is data processing framework that is extensible and allows user to add new module for any other processing.

[Here](https://github.com/Open-Speech-EkStep/audio-to-speech-pipeline) is the code  
In this project we have some module:


## Architecture

![Architecture](img/intelligent_pipeline_arch.png)

## Steps:
 * [Audio processor](#audio-processor)
 * [Speaker identification](#speaker)
 * [Gender identification](#gender)
 * [Audio transcription (STT)](#STT)


The developer documentation helps you to get familiar with the bare necessities, giving you a quick and clean approach to get you up and running. If you are looking for ways to customize the workflow, or just breaking things down to build them back up, head to the reference section to dig into the mechanics of Data Pipelines.


#### * Audio processor
![Screenshot](img/data_pipeline.png)

In audio processor we run Vad to break down audio in utterances after that we run WADASNR to calculate SNR for each utterance. 
#### * Speaker identification
This module is for identity the number of speaker for a given source. we are using resemblyzer.

#### * Gender identification
This module is for identity the gender of speaker in a utterance.

### Data Marker (with config):
This module is for filtering out data for STT after analyzing that data we have different kind of filter eg: SNR,Duration,speaker...

#### * Audio transcription (STT)
This module is for run Speech To Text (STT) in given source. we can use google,azure and we can also add more API for running STT.

<!-- GETTING STARTED -->
## Getting Started

To get started install the prerequisites and clone the repo to machine on which you wish to run the framework.

### Installation

1. Clone the repo
```sh
git clone git@github.com:Open-Speech-EkStep/audio-to-speech-pipeline.git
```
2. Install python requirements
```sh
pip install -r requirements.txt
```

## Run on GCP (with composer)
  ### Requirements: 
   1. Terraform [https://www.terraform.io/downloads.html](https://www.terraform.io/downloads.html)

   2. gcloud [https://cloud.google.com/sdk/docs/install](https://cloud.google.com/sdk/docs/install)

  ### Infra Setup (with config):

  1. Clone the repo:
    ```sh git clone git@github.com:Open-Speech-EkStep/audio-to-speech-pipeline.git```
  
  2. Initialize terraform modules  
  ```terraform init```  

  3. Select a workspace as per the environments(dev,test,prod).  
  ```terraform workspace select <env_name>```  
  eg: ```terraform workspace select prod``` 

  4. Run specific modules as per requirements.  
  ```sh terraform apply -target=module.<module-name>```   
  eg: ```sh terraform apply -target=module.sql-database```  

  5. Run all modules at once.  
  ```terraform apply```    
  
  ### CI/CD setup:

   Once you pull code you have to configure some variable in your [circle-ci](https://circleci.com/ "circle-ci").
   So that while deploying code image should easily push into google container registry.
   
   ```
    1. GCP_PROJECT # Name of your GCP project
    2. GOOGLE_AUTH # Service account key that is created using terraform
    3. POSTGRES_DB # Database host ip that is created using terraform
    4. POSTGRES_PASSWORD  # Database password
    5. POSTGRES_USER # Database user name
   ```
  
  ### Audio Processing (with config):
   #### Description:
   
   #### Config:
    ```
      config:
        common:
          db_configuration:
              db_name: ''
              db_pass: ''
              db_user: ''
              cloud_sql_connection_name: '<DB Host>'

          gcs_config:
            # master data bucket
            master_bucket: '<Name of the bucket>'

        audio_processor_config:

          # feat_language_identification should true if you want run language identification for a source
          feat_language_identification: False 
          # language of the audio 
          language: '' 

          # path of the files on gcs which need to be processed
          # path eg: <bucket-name/data/audiotospeech/raw/download/downloaded/{language}/audio>
          remote_raw_audio_file_path: ''

          # after processing where we want to move raw data
          snr_done_folder_path: '' # <bucket-name/data/audiotospeech/raw/download/snr_done/{language}/audio>

          # path where the processed files need to be uploaded
          remote_processed_audio_file_path: '' # <bucket-name/data/audiotospeech/raw/download/catalogue/{language}/audio>

          # path where Duplicate files need to be uploaded based on checksum
          duplicate_audio_file_path: '' # <bucket-name/data/audiotospeech/raw/download/duplicate/{language}/audio>

          chunking_conversion_configuration:
            aggressiveness: '' # using for vad by default it's value is 2 the more the value that aggressive vad for chunking audio 
            max_duration: ''   # max duration is second if chunk is more than that vad will retry chunking with inc aggressiveness 

          # SNR specific configurations
          snr_configuration:

            max_snr_threshold: '' # less than max_snr_threshold utterance will move to rejected folder.
            local_input_file_path: ''
            local_output_file_path: ''
    ```

#### steps to run: 

1. We have to configure **sourcepathforsnr** in airflow variable where our raw data stored.

2. Other variable is **snrcatalogue** in that we update our source which we want to run and count how many file should run 
in one trigger.and format is what raw audio file format in bucket and language and parallelism is how many pod will up in one
run if parallelism is not define number of pod = count ex:

 ```
 "snrcatalogue": {
    "<source_name>": {
    "count": 5,
    "format": "mp3",
    "language": "telugu",
    "parallelism":2
}
```

3. We have to also set **audiofilelist** with whatever source we want to run with empty array that will store our file path ex:

 ``` 
 "audiofilelist": {
      "<source_name>": []
 }
 ```

4. That will create a dag with the source_name now we can trigger that dag that will process given number(count) of file.
       and upload processed file to **remote_processed_audio_file_path** that we mentioned in config file. and move raw data from 
       **remote_raw_audio_file_path** to **snr_done_folder_path**. and update DB also with the metadata which we created using circle-ci.


### Audio Analysis (with config)
    
##### Description:

  In audio analysis we have two module gender_analysis and speaker_analysis.
  in speaker analysis we can find how many uniq speaker is present in given source.
  and gender_analysis we can find which utterance is spoked by which gender

#### Config:

  ```
    audio_analysis_config:

    analysis_options:

      gender_analysis: 1 # It should be 1 or 0 if you want to run gender_analysis it should be 1 else 0
      speaker_analysis: 0 # It should be 1 or 0 if you want to run speaker_analysis it should be 1 else 0

    # path where the processed files need to be uploaded
    remote_processed_audio_file_path: '<bucket_name>/data/audiotospeech/raw/download/catalogued/{language}/audio'
    
    # speaker_analysis_config it's for gender_analysis module
    speaker_analysis_config:

      min_cluster_size: 4 # min_cluster_size is least number of cluster for one speaker
      partial_set_size: 8000 # number of utterances for create embeddings for a given source 
      fit_noise_on_similarity: 0.77 
      min_samples: 2 
  ```

#### steps to run: 

1. We have to configure **audio_analysis_config** in airflow variable in this json we have to mention source name and language.

```
"audio_analysis_config" : {
    "<source name>" : {
    "language" : "hindi"
     }
}
```

2. That will create a dag audio_analysis now we can trigger that dag that will process given sources.
       and upload processed file to **remote_processed_audio_file_path** that we mentioned in config file.
       and update DB also with the metadata which we created using circle-ci.


### Data Balancing (with config):
#### config:
    ```
    data_tagger_config:
    # path of to the folder in the master bucket where the data tagger will move the data to
    landing_directory_path: '' #'<bucket_name>/data/audiotospeech/raw/download/catalogued/{language}/audio'

    # path of to the folder in the master bucket from where the data tagger will pick up the data that needs to be moved
    source_directory_path: '' #'<bucket_name>/data/audiotospeech/raw/landing/{language}/audio'
    ```

#### steps to run: 
1. We need to configure **data_filter_config** airflow variable for each source. we have multiple filters 
 1. **by_snr** # filter based on SNR value
 2. **by_duration** # total duration from a given source.
 3. **by_speaker** # we can configure how much data per speaker we want.
 4. **by_utterance_duration** # we can required duration of utterance.
 5. **exclude_audio_ids** # we can pass a list of audio_ids that we want to skip.
 6. **exclude_speaker_ids** # we can pass a list of speaker_ids that we want to skip.
 7. **with_randomness** # It is a boolean value if it's it will pickup random data from DB.

```
"data_filter_config": {
    "test_source1": {
      "language": "hindi",
      "filter": {
        "by_snr": {
          "lte": 75,
          "gte": 15
        },
        "by_duration": 2,
        "with_randomness": "true"
      }
    },
    "test_source2": {
      "language": "hindi",
      "filter": {
        "by_speaker": {
          "lte_per_speaker_duration": 60,
          "gte_per_speaker_duration": 0,
          "with_threshold": 0
        },
        "by_duration": 2
      }
    }
```
2. After configure all value one dag will created **data_marker_pipeline** we can trigger that dag. this dag filter out all data 
from given criteria It will pick data from **source_directory_path** and after filtering move data to **landing_directory_path**.
### Audio Transcription (with config):
#### config:

  ```
    config:
      common:

        db_configuration:
            db_name: ''
            db_pass: ''
            db_user: ''
            cloud_sql_connection_name: '<DB host>'

        gcs_config:
          # master data bucket 
          master_bucket: '<bucket name>'

        azure_transcription_client:
          speech_key: '<key of the api>'
          service_region: 'centralindia' # service region

        google_transcription_client:
          bucket: '<bucket name>'
          language: 'hi-IN' # It is BCP-47 language tag with this we call STT api.
          sample_rate: 16000 # Sample rate of audio utterance
          audio_channel_count: 1 #The number of channels in the input audio data

      audio_transcription_config:
      # defaults to hi-IN

      language: 'hi-IN' # language 

      # audio_language it's used for sanitization rule whichever language you choose you need to add a rule class for the same.
      # You can use reference of hindi sanitization
      # sanitization rule eg: empty transcription, strip, char etc

      audio_language: 'kannada' 

      # Bucket bath of wav file 
      remote_clean_audio_file_path: '<bucketname>/data/audiotospeech/raw/landing/{language}/audio'

      # path where the processed files need to be uploaded
      remote_stt_audio_file_path: '<bucketname>/data/audiotospeech/integration/processed/{language}/audio'

  ```
#### steps to run: 

1. We have to configure **sttsourcepath** in airflow variable where our raw data stored.

2. Other variable is **sourceinfo** in that we update our source which we want to run for STT and count how many file should run 
in one trigger.stt is whatever api we want to call for STT for google and azure we have all rapper for other API you can add rapper as well. language and parallelism is how many pod will up in one
run if parallelism is not define number of pod = count ex:

```
 "snrcatalogue": {
    "<source_name>": {
    "count": 5,
    "stt":"google"
    "language": "telugu",
    "parallelism":2
  }
```

3. We have to also set **audioidsforstt** and **integrationprocessedpath** with whatever source we want to run with empty array that will store audio_id ex:

 ``` 
 "audioidsforstt": {
      "<source_name>": []
 }
 ```

 ```
 integrationprocessedpath:"" # path of folder where we want move transcribed data.
 ```

4. That will create a dag with the source_name now we can trigger that dag that will process given number(count) of file.
and upload processed file to **remote_stt_audio_file_path** that we mentioned in config file. and move raw data from 
         **remote_clean_audio_file_path** to **integrationprocessedpath**. and update DB also with the metadata which we created using circle-ci.


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

<!-- CONTACT -->
## Contact

Your Name - [@your_twitter](https://twitter.com/your_username) - email@example.com

Project Link: [https://github.com/Open-Speech-EkStep/audio-to-speech-pipeline](https://github.com/Open-Speech-EkStep/audio-to-speech-pipeline)

<!-- ACKNOWLEDGEMENTS -->

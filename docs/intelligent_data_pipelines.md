# Data pipelines

![Screenshot](img/data_pipeline.png)
<!-- TABLE OF CONTENTS -->
## Table of Contents

* [About the Project](#about-the-project)
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
   1.Terraform [https://www.terraform.io/downloads.html](https://www.terraform.io/downloads.html)

   2.gcloud [https://cloud.google.com/sdk/docs/install](https://cloud.google.com/sdk/docs/install)

  ### Infra Setup (with config):

  1. Clone the repo
    ```sh
    git clone git@github.com:Open-Speech-EkStep/audio-to-speech-pipeline.git
    ```
  2.Initialize terraform modules  
  ```terraform init```  

  3. Select a workspace as per the environments(dev,test,prod).  
  ```terraform workspace select <env_name>```  
  eg: ```terraform workspace select prod``` 

  4. Run specific modules as per requirements.  
  ```terraform apply -target=module.<module-name>```   
  eg: ```terraform apply -target=module.sql-database```  

  5. Run all modules at once.  
  ```terraform apply```    
  
  * CI/CD setup:

   Once we deploy using circle-ci it will create schema for our DB and upload our dags in airflow dag bucket that we create using
   terraform. and also create and push image of code in google container registry and upload our variable to airflow.
  
  ### Audio Processing (with config):
   description:
   config:
    ![Screenshot](img/audio_processor_config.png)

   steps to run : 

    1. We have to configure *sourcepathforsnr* in airflow variable where our raw data stored.

    2. Other variable is *snrcatalogue* in that we update our source which we want to run and count how many file should run 
       in one trigger.and format is what raw audio file format in bucket and language and parallelism is how many pod will up in one
       run if parallelism is not define number of pod = count ex:
       ```"snrcatalogue": {
          "<source_name>": {
          "count": 5,
          "format": "mp3",
          "language": "telugu",
          "parallelism":2
      }```

    3. We have to also set *audiofilelist* with whatever source we want to run with empty array that will store our file path ex:

       ``` "audiofilelist": {
            "<source_name>": []
       }```
    
    4. That will create a dag with the source_name now we can trigger that dag that will process given number(count) of file.
       and upload processed file to *remote_processed_audio_file_path* that we mentioned in config file. and move raw data from 
       *remote_raw_audio_file_path* to *snr_done_folder_path*. and update DB also with the metadata which we created using circle-ci.
       

  ### Audio Analysis (with config)
 
  ### Data Balancing (with config):
   
  ### Audio Transcription (with config)


<!-- CONTRIBUTING -->
## Contributing

Contributions are what make the open source community such an amazing place to be learn, inspire, and create. Any contributions you make are **greatly appreciated**.

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request



<!-- LICENSE -->
## License

Distributed under the [XYZ] License. See `LICENSE` for more information.



<!-- CONTACT -->
## Contact

Your Name - [@your_twitter](https://twitter.com/your_username) - email@example.com

Project Link: [https://github.com/your_username/repo_name](https://github.com/your_username/repo_name)



<!-- ACKNOWLEDGEMENTS -->

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
   3.ffmpeg [https://www.ffmpeg.org](https://www.ffmpeg.org)

  * Infra Setup (with config):
    
  

  * CI/CD setup:
  
  ### Audio Processing (with config):
   description:
   config:
   steps to run : 

  ### Audio Analysis (with config)
 
  ### Data Balancing (with config):
   
  ### Audio Transcription (with config)
  
### Prerequisites
1. ffmpeg
* Any linux based (preferred Ubuntu)
```sh
sudo apt-get install ffmpeg
```
* Mac-ox
```sh
brew install ffmpeg
```
2. Supported Python Version = 3.6

* Windows user can follow installation steps on [https://www.ffmpeg.org](https://www.ffmpeg.org)


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

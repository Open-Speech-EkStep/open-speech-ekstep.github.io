# Smart Crawlers

![Screenshot](img/data_pipelines.png)
<!-- TABLE OF CONTENTS -->
## Table of Contents

* [About the Project](#about-the-project)
  * [Built With](#built-with)
* [Getting Started](#getting-started)
  * [Prerequisites](#prerequisites)
  * [Installation](#installation)
* [Contributing](#contributing)
* [License](#license)
* [Contact](#contact)
* [Acknowledgements](#acknowledgements)



<!-- ABOUT THE PROJECT -->
## About The Project

This is data processing framework that is extensible and allows user to add new module for any other processing. In this project we have some module:

* Audio processor (Vad,SNR,language identification)
* Speaker identification
* Gender identification
* Audio transcription (STT)

In audio processor we run Vad to break down audio in utterances after that we run WADASNR to calculate SNR for each utterance. 

The developer documentation helps you to get familiar with the bare necessities, giving you a quick and clean approach to get you up and running. If you are looking for ways to customize the workflow, or just breaking things down to build them back up, head to the reference section to dig into the mechanics of Data Pipelines.


### Built With
We have used some tool as the base of this framework.
* [webrtcvad](https://pypi.org/project/webrtcvad/)
* [WadaSNR]

<!-- GETTING STARTED -->
## Getting Started

To get started install the prerequisites and clone the repo to machine on which you wish to run the framework.

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
### Installation

1. Clone the repo
```sh
git clone git@github.com:Open-Speech-EkStep/audio-to-speech-pipeline.git
```
2. Install python requirements
```sh
pip install -r requirements.txt
```


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

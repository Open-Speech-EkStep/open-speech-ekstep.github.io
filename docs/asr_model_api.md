# Speech Recognition model API


<!-- ABOUT THE PROJECT -->
## About The Project
Our speech to text interface enables you to accurately convert speech into text using an API powered by deep learning neural network algorithms for automatic speech recognition (ASR).

This is enabled to provide the following features:

* Speech to text transcription support for a growing list of indic languages.

* Transcribe your content in real time from stored files or audio bytes.

* Generate subtitle or transcript for your audios as per your choice of output.

* Support for various audio formats like WAV,MP3,PCM.

* [beta]Enables transcription optimized for domain-specific quality requirements associating domain models in backend.

* [beta]Speech-to-Text accurately punctuates transcriptions (e.g., commas, question marks, and periods).


The Developer documentation provides you with a complete set of guidelines which you need to get started with:

* Architecture overview
* API reference  
* Client Code reference
* Setup and getting started guide
* Extend this project
* Contribute to this project

## Architecture Overview

![Screenshot](img/open-api.png)

The logical architecture here is built with a grpc server hosting our speech recognition models and dependencies, which cna be run in any environment or docker.
With gRPC we can define our service once in a .proto file and generate clients and servers in any of gRPC’s supported languages, which in turn can be run in environments ranging from servers inside a large data center to your own tablet — all the complexity of communication between different languages and environments is handled for you by gRPC. We also get all the advantages of working with protocol buffers, including efficient serialization, a simple IDL, and easy interface updating.
In gRPC, a client application can directly call a method on a server application on a different machine as if it were a local object, making it easier for you to create distributed applications and services. Here we can use the grpc generated stubs from client code in any language and make requests using gRPC calls and receive the responses from the server.On the server side, the server implements this interface and runs a gRPC server to handle client calls.
By default, gRPC uses Protocol Buffers, Google’s mature open source mechanism for serializing structured data (although it can be used with other data formats such as JSON).
gRPC uses protoc with a special gRPC plugin to generate code from your proto file: you get generated gRPC client and server code, as well as the regular protocol buffer code for populating, serializing, and retrieving your message types.
Apart from using gRPC stubs, we have added the support for REST calls to the gRPC server via an api-gateway.
With API Gateway for gRPC, you can use the API management capabilities of API Gateway to add monitoring, hosting, tracing, authentication, and more to your gRPC services on Cloud Run. In addition, once you specify special mapping rules, API Gateway translates RESTful JSON over HTTP into gRPC requests. This means that you can deploy a gRPC server managed by API Gateway and call its API using a gRPC or JSON/HTTP client, giving you much more flexibility and ease of integration with other systems.

## API reference

Our API has predictable resource-oriented URLs, accepts form-encoded request bodies, returns JSON-encoded responses, and uses standard HTTP response codes, authentication, and verbs.

**Base URL**
```
https://<gateway-url>/v1/recognize/
```
**Authentication**

Authentication to the API is performed via HTTP Basic Auth. Provide your API key as the basic auth username value. You do not need to provide a password.
The Stripe API uses API keys to authenticate requests. If you need to authenticate via bearer auth (e.g., for a cross-origin request), use -H "Authorization: Bearer sk_test_4eC39HqLyjWDarjtT1zdp7dc"
As of now all API requests can be made over HTTPS or HTTP both. Calls made over plain HTTP will fail going ahead.

**Errors**

Our API uses HTTP response codes to indicate the success or failure of an API request.
```
200 - OK	Everything worked as expected.
400 - Bad Request	The request was unacceptable, often due to missing a required parameter.
401 - Unauthorized	No valid API key provided.
402 - Request Failed	The parameters were valid but the request failed.
403 - Forbidden	The API key doesn't have permissions to perform the request.
404 - Not Found	The requested resource doesn't exist.
409 - Conflict	The request conflicts with another request (perhaps due to using the same idempotent key).
429 - Too Many Requests	Too many requests hit the API too quickly. We recommend an exponential backoff of your requests.
500, 502, 503, 504 - Server Errors	Something went wrong on Stripe's end. (These are rare.)
```
**Handling errors**

We are in process of writing code that gracefully handles all possible API exceptions. This is something work in progress and will be available soon.

**Endpoints Supported**

The recognize object
```
POST /v1/recognize/
```
**Request Attributes**
```
config - Holds the configuration objects for language,transcriptionFormat and audio format.
```
Child Attributes:

        
        language : REQUIRED - Specify the value of the language and its attributes
        
        Child Attributes:
        
            value : string : REQUIRED - Specify a langauge code for the audio transcription.
                            Enum: ['en', 'hi', 'ta', 'te', 'kn', 'or', 'gu', 'en-IN']
     
        transcriptionFormat : string : OPTIONAL - Determine the output format as either SRT or TRANSCRIPT.Default value is TRANSCRIPT.
                                       Enum : ['SRT','TRANSCRIPT']
     
        audioFormat : string : OPTIONAL - Determine the input audio formats from the list supported.Default value is WAV. 
                               Enum : ['WAV', 'MP3', 'PCM']

```
audio - Specify the audio properties configuration. Either provide the audio URL or the audio bytes.
```
Child Attributes:

Either of the below attributes is REQUIRED.

        audioUri : string : REQUIRED - Specify the audio URL path
        
        audioContent : string : REQUIRED - Specify the byte representation of the audio as part of the request.

*Request body Example Schema* 

```
{
    "config": {
        "language": {
            "value": "hi"
        },
        "transcriptionFormat": "SRT",
        "audioFormat": "WAV"
    },
    "audio": {
        "audioUri": "https://codmento.com/ekstep/test/changed.wav"
    }
}
```
**Responses**

```
Code	Description
200     On successful completion of the job.
```
   
*Response Attributes*

```
srt : string - The subtitle as output if transcription format is chosen at SRT.
```
```
transcript : string - The transcript as output if transcription format is chosen at TRANSCRIPT.
```
*Response body Example Schema* 
```
{
    "srt": "1\n00:00:01,29 --> 00:00:04,88\nहिंदी मॉडल टेस्ट कर रही हूं\n\n2\n00:00:05,00 --> 00:00:09,89\nकैसा चल रहा है मेरे विंडोज लैपटॉप से\n\n"
}
```
```
{
    "transcript": "हिंदी मॉडल टेस्ट कर रही हूं कैसा चल रहा है मेरे विंडोज लैपटॉप से"
}
```
**Responses**
```
Code	Description
400     On input errors causing a failure in the job.
```
*Response Attributes*
```
code : string - status code as encountered in the processing life-cycle.
```
```
message : string - human understandable format.
```
*Response body Example Schema* 
```
{
    "code": 400,
    "message": "config.audioFormat: invalid value \"MP4\" for type type.googleapis.com/ekstep.speech_recognition.RecognitionConfig.AudioFormat"
}
```
**Responses**
```
Code	Description
500     Internal error causing a failure in the job.
```
*Response Attributes*
```
code : string - status code as encountered in the processing life-cycle.
```
```
message : string - human understandable format.
```
*Response body Example Schema* 
```
{
    "code": 2,
    "message": "Exception calling application: An unknown error has occurred.Please try again."
}
```

**Sample Request**
```
curl --location --request POST 'https://<gateway-url>/v1/recognize/' \
--header 'Content-Type: text/plain' \
--data-raw '{ "config": { "language": { "value": "hi" }, "transcriptionFormat": "SRT", "audioFormat": "WAV" }, "audio": { "audioUri": "https://codmento.com/ekstep/test/changed.wav" } }'
```

**Sample Response**
```
{
    "srt": "1\n00:00:01,29 --> 00:00:04,88\nहिंदी मॉडल टेस्ट कर रही हूं\n\n2\n00:00:05,00 --> 00:00:09,89\nकैसा चल रहा है मेरे विंडोज लैपटॉप से\n\n"
}
```

**Sample Request**
```
curl --location --request POST 'https://<gateway-url>/v1/recognize/' \
--header 'Content-Type: text/plain' \
--data-raw '{ "config": { "language": { "value": "hi" }, "transcriptionFormat": "TRANSCRIPT", "audioFormat": "WAV" }, "audio": { "audioUri": "https://codmento.com/ekstep/test/changed.wav" } }'
```

**Sample Response**

```
{
    "transcript": "हिंदी मॉडल टेस्ट कर रही हूं कैसा चल रहा है मेरे विंडोज लैपटॉप से"
}
```

## Client Code reference

We provide  client libraries as stubs for different programming languages.

python

```
import grpc
from stub.speech_recognition_open_api_pb2_grpc import SpeechRecognizerStub
from stub.speech_recognition_open_api_pb2 import Language, RecognitionConfig, RecognitionAudio, \
    SpeechRecognitionRequest
import wave
from grpc_interceptor import ClientCallDetails, ClientInterceptor


class GrpcAuth(grpc.AuthMetadataPlugin):
    def __init__(self, key):
        self._key = key

    def __call__(self, context, callback):
        callback((('rpc-auth-header', self._key),), None)


class MetadataClientInterceptor(ClientInterceptor):

    def __init__(self, key):
        self._key = key

    def intercept(
            self,
            method,
            request_or_iterator,
            call_details: grpc.ClientCallDetails,
    ):
        new_details = ClientCallDetails(
            call_details.method,
            call_details.timeout,
            [("authorization", "Bearer " + self._key)],
            call_details.credentials,
            call_details.wait_for_ready,
            call_details.compression,
        )

        return method(request_or_iterator, new_details)


def read_audio():
    with wave.open('changed.wav', 'rb') as f:
        return f.readframes(f.getnframes())


def transcribe_audio_bytes(stub):
    language = "hi"
    audio_bytes = read_audio()
    lang = Language(value=language, name='Hindi')
    config = RecognitionConfig(language=lang, audioFormat='WAV', transcriptionFormat='TRANSCRIPT')
    audio = RecognitionAudio(audioContent=audio_bytes)
    request = SpeechRecognitionRequest(audio=audio, config=config)

    # creds = grpc.metadata_call_credentials(
    #     metadata_plugin=GrpcAuth('access_key')
    # )
    response = stub.recognize(request)

    print(response.transcript)


def transcribe_audio_url(stub):
    language = "hi"
    url = "https://codmento.com/ekstep/test/changed.wav"
    lang = Language(value=language, name='Hindi')
    config = RecognitionConfig(language=lang, audioFormat='WAV')
    audio = RecognitionAudio(audioUri=url)
    request = SpeechRecognitionRequest(audio=audio, config=config)

    response = stub.recognize(request)

    print(response.transcript)


def get_srt_audio_bytes(stub):
    language = "hi"
    audio_bytes = read_audio()
    lang = Language(value=language, name='Hindi')
    config = RecognitionConfig(language=lang, audioFormat='WAV', transcriptionFormat='SRT')
    audio = RecognitionAudio(audioContent=audio_bytes)
    request = SpeechRecognitionRequest(audio=audio, config=config)

    # creds = grpc.metadata_call_credentials(
    #     metadata_plugin=GrpcAuth('access_key')
    # )
    response = stub.recognize(request)

    print(response.srt)


def get_srt_audio_url(stub):
    language = "hi"
    url = "https://codmento.com/ekstep/test/changed.wav"
    lang = Language(value=language, name='Hindi')
    config = RecognitionConfig(language=lang, audioFormat='WAV', transcriptionFormat='SRT')
    audio = RecognitionAudio(audioUri=url)
    request = SpeechRecognitionRequest(audio=audio, config=config)

    response = stub.recognize(request)

    print(response.srt)


if __name__ == '__main__':
    key = "mysecrettoken"
    interceptors = [MetadataClientInterceptor(key)]
    with grpc.insecure_channel('34.70.114.226:50051') as channel:
        channel = grpc.intercept_channel(channel, *interceptors)
        stub = SpeechRecognizerStub(channel)
        transcribe_audio_url(stub)
        transcribe_audio_bytes(stub)
        get_srt_audio_url(stub)
        get_srt_audio_bytes(stub)

```

Java
```
package com.ekstep.endpoints.speech_recognition;

import com.google.protobuf.ByteString;
import io.grpc.Channel;
import io.grpc.ManagedChannel;
import io.grpc.ManagedChannelBuilder;
import io.grpc.StatusRuntimeException;

import java.util.concurrent.TimeUnit;
import java.util.logging.Level;
import java.util.logging.Logger;

public class SpeechRecognitionClient {
    private static final Logger logger = Logger.getLogger(SpeechRecognitionClient.class.getName());

    private final SpeechRecognizerGrpc.SpeechRecognizerBlockingStub blockingStub;

    public SpeechRecognitionClient(Channel channel) {
        blockingStub = SpeechRecognizerGrpc.newBlockingStub(channel);
    }

    public SpeechRecognitionResult transcribeUrlV2() {
        String audioUrl = "https://codmento.com/ekstep/test/changed.wav";
        logger.info("Will try to request " + audioUrl + " ...");
        RecognitionConfig config = RecognitionConfig.newBuilder()
                .setLanguage(Language.newBuilder().setValue(Language.LanguageCode.hi).build())
                .setAudioFormat(RecognitionConfig.AudioFormat.WAV)
                .build();
        RecognitionAudio audio = RecognitionAudio.newBuilder().setAudioUri(audioUrl).build();
        SpeechRecognitionRequest request = SpeechRecognitionRequest.newBuilder()
                .setAudio(audio)
                .setConfig(config)
                .build();
        SpeechRecognitionResult response;

        try {
            response = blockingStub.recognize(request);
            return response;
        } catch (StatusRuntimeException e) {
            logger.log(Level.WARNING, "RPC failed: {0}", e.getStatus());
            return SpeechRecognitionResult.newBuilder().build();
        }
    }

    public SpeechRecognitionResult transcribeBytesV2() {
        logger.info("Will try to request ...");
        AudioFiles audioFiles = new AudioFiles();
        String file = "/Users/nireshkumarr/Documents/ekstep/speech-recognition-open-api/examples/python/speech-recognition/changed.wav";
        byte[] data2 = audioFiles.readAudioFileData(file);
        ByteString byteString = ByteString.copyFrom(data2);

        RecognitionConfig config = RecognitionConfig.newBuilder()
                .setLanguage(Language.newBuilder().setValue(Language.LanguageCode.hi).build())
                .setAudioFormat(RecognitionConfig.AudioFormat.WAV)
                .build();
        RecognitionAudio audio = RecognitionAudio.newBuilder().setAudioContent(byteString).build();
        SpeechRecognitionRequest request = SpeechRecognitionRequest.newBuilder()
                .setAudio(audio)
                .setConfig(config)
                .build();

        SpeechRecognitionResult response;
        try {
            response = blockingStub.recognize(request);
            return response;
        } catch (StatusRuntimeException e) {
            logger.log(Level.WARNING, "RPC failed: {0}", e.getStatus());
            return SpeechRecognitionResult.newBuilder().build();
        }
    }

    public SpeechRecognitionResult srtUrlV2() {
        String audioUrl = "https://codmento.com/ekstep/test/changed.wav";
        logger.info("Will try to request " + audioUrl + " ...");
        RecognitionConfig config = RecognitionConfig.newBuilder()
                .setLanguage(Language.newBuilder().setValue(Language.LanguageCode.hi).build())
                .setAudioFormat(RecognitionConfig.AudioFormat.WAV)
                .setTranscriptionFormat(RecognitionConfig.TranscriptionFormat.SRT)
                .build();
        RecognitionAudio audio = RecognitionAudio.newBuilder().setAudioUri(audioUrl).build();
        SpeechRecognitionRequest request = SpeechRecognitionRequest.newBuilder()
                .setAudio(audio)
                .setConfig(config)
                .build();
        SpeechRecognitionResult response;

        try {
            response = blockingStub.recognize(request);
            return response;
        } catch (StatusRuntimeException e) {
            logger.log(Level.WARNING, "RPC failed: {0}", e.getStatus());
            return SpeechRecognitionResult.newBuilder().build();
        }
    }



    public SpeechRecognitionResult srtBytesV2() {
        logger.info("Will try to request ...");
        AudioFiles audioFiles = new AudioFiles();
        String file = "/Users/nireshkumarr/Documents/ekstep/speech-recognition-open-api/examples/python/speech-recognition/changed.wav";
        byte[] data2 = audioFiles.readAudioFileData(file);
        ByteString byteString = ByteString.copyFrom(data2);

        RecognitionConfig config = RecognitionConfig.newBuilder()
                .setLanguage(Language.newBuilder().setValue(Language.LanguageCode.hi).build())
                .setAudioFormat(RecognitionConfig.AudioFormat.WAV)
                .setTranscriptionFormat(RecognitionConfig.TranscriptionFormat.SRT)
                .build();
        RecognitionAudio audio = RecognitionAudio.newBuilder().setAudioContent(byteString).build();
        SpeechRecognitionRequest request = SpeechRecognitionRequest.newBuilder()
                .setAudio(audio)
                .setConfig(config)
                .build();

        SpeechRecognitionResult response;
        try {
            response = blockingStub.recognize(request);
            return response;
        } catch (StatusRuntimeException e) {
            logger.log(Level.WARNING, "RPC failed: {0}", e.getStatus());
            return SpeechRecognitionResult.newBuilder().build();
        }
    }

    public static void main(String[] args) throws Exception {
        String target = "34.70.114.226:50051";

        ManagedChannel channel = ManagedChannelBuilder.forTarget(target)
                .usePlaintext()
                .build();
        try {
            SpeechRecognitionClient client = new SpeechRecognitionClient(channel);
            SpeechRecognitionResult srtUrlResponse = client.srtUrlV2()
            SpeechRecognitionResult srtBytesResponse = client.srtBytesV2();
            SpeechRecognitionResult bytesResponse = client.transcribeBytesV2();
            SpeechRecognitionResult urlResponse = client.transcribeUrlV2();
            System.out.println(bytesResponse.getTranscript());
            System.out.println(urlResponse.getTranscript());
            System.out.println(srtBytesResponse.getSrt());
            System.out.println(srtUrlResponse.getSrt());

        } finally {
            channel.shutdownNow().awaitTermination(5, TimeUnit.SECONDS);
        }
    }
}

```
## Setup and getting started guide

Clone our github repo : https://github.com/Open-Speech-EkStep/speech-recognition-open-api.git

Setup the grpc server:

**Without docker**
1. Create and activate a new environment :

    ```conda create --name <env> python=3.8 && conda activate <env>```

2. Install required libraries using the following command:

    ```
    pip install -r requirements.txt
    ```

3. Bootstrap the model code and other models as pre requisites:

    ```
    sh model_bootstrap.sh
    ```
4. Download models and update the right model paths in model_dict.json.
5. Start the server at port 50051:

    ```
    python server.py
    ```
**With docker**

```
docker build -t speech_recognition_model_api .
```

```
sudo docker run --cpus=6 -m 20000m -itd -p <<host_port>>:50051 --name speech_recognition_model_api -v <<host_model_path>>/deployed_models:<<container_model_path>>/deployed_models/ -i -t speech_recognition_model_api
```

Using the model api as part of client code:

In python,
```
python examples/python/speech-recognition/main.py
```

Using the model api as part of REST call using api-gateway:

Create api config in api gateway:
```
gcloud api-gateway api-configs create CONFIG_ID \
--api=API_ID --project=PROJECT_ID \
--grpc-files=api_descriptor.pb,api_config.yaml
```

Deploy gateway in api gateway:
```
gcloud api-gateway gateways create GATEWAY_ID \
  --api=API_ID --api-config=CONFIG_ID \
  --location=GCP_REGION --project=PROJECT_ID
```
View gateway information:
```
gcloud api-gateway gateways describe GATEWAY_ID \
  --location=GCP_REGION --project=PROJECT_ID
```

To run tests, use the following command:
```
py.test --grpc-fake-server --ignore=wav2letter --ignore=wav2vec-infer --ignore=kenlm
```

## Contributing

Contributions are what make the open source community such an amazing place to be learn, inspire, and create. Any contributions you make are **greatly appreciated**.

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## License

Distributed under the [MIT] License. See `LICENSE` for more information.

## Git repository

[https://github.com/Open-Speech-EkStep/speech-recognition-open-api.git](https://github.com/Open-Speech-EkStep/speech-recognition-open-api.git)

## Contact

Connect with community on [Gitter](https://gitter.im/Vakyansh/community?utm_source=share-link&utm_medium=link&utm_campaign=share-link)

Project Link: [https://github.com/Open-Speech-EkStep/speech-recognition-open-api.git](https://github.com/Open-Speech-EkStep/speech-recognition-open-api.git)




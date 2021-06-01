# Speech Recognition model API


<!-- TABLE OF CONTENTS -->




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
https://<agteway-url>/v1/recognize/
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
Child parameters:

   
        language - Can hold language codes supported.Valid options are ['en', 'hi', 'ta', 'te', 'kn', 'or', 'gu', 'en-IN']
     
        transcriptionFormat - Determine the output format as either SRT or TRANSCRIPT.Default value is TRANSCRIPT.
     
        audioFormat - Determine the input audio formats supported like ['WAV', 'MP3', 'PCM'].Default value is WAV. 

```
audio - Specify the audio properties configuration. Either provide the audio URL or the audio bytes.
```
Child parameters:

        audioUri - Specify the audio URL path
        
        audioContent - Specify the byte representation of the audio as part of the request.

**Response Attributes**
```
srt - The subtitle as output if transcription format is chosen at SRT.
```
```
transcript - The transcript as output if transcription format is chosen at TRANSCRIPT.
```

**Sample Request**
```
curl --location --request POST 'https://speech-recog-model-api-gateway-h3dqga4.ue.gateway.dev/v1/recognize/' \
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
curl --location --request POST 'https://speech-recog-model-api-gateway-h3dqga4.ue.gateway.dev/v1/recognize/' \
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

TBD



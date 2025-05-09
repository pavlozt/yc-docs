# Example use of streaming recognition with API v2


The example shows how you can recognize speech in LPCM format in real time using the {{ speechkit-short-name }} [API v2](streaming-api.md).

The example uses the following parameters:

* [Language](../index.md#langs): Russian.
* Format of the audio stream: [LPCM](../../formats.md#LPCM) with a sampling rate of 8000 Hz.
* [Profanity filter](streaming-api.md#specification-msg): True.
* [Intermediate result filter](streaming-api.md#specification-msg): True.
* Other parameters are left at their defaults.

To use the API, you need the `grpcio-tools` package for Python and `grpc` for Node.js.

The Yandex account or federated account are authenticated using an [IAM token](../../../iam/concepts/authorization/iam-token.md). If you are using your service account, you do not need to include the folder ID in the request header. Learn more about [authentication in the {{ speechkit-name }} API](../../concepts/auth.md).

To try the examples in this section:

1. Clone the [{{ yandex-cloud }} API](https://github.com/yandex-cloud/cloudapi) repository:

    ```bash
    git clone https://github.com/yandex-cloud/cloudapi
    ```

1. Download a [sample](https://{{ s3-storage-host }}/speechkit/speech.pcm) audio file for recognition.

1. Create a client application:

   {% list tabs group=programming_language %}

   - Python 3 {#python}

     1. Use the [pip](https://pip.pypa.io/en/stable/) package manager to install the `grpcio-tools` package:

        ```bash
        pip install grpcio-tools
        ```

     1. Go to the folder with the [{{ yandex-cloud }} API](https://github.com/yandex-cloud/cloudapi/) repository, create a folder named `output` and generate the client interface code in it:

        ```bash
        cd cloudapi
        mkdir output
        python3 -m grpc_tools.protoc -I . -I third_party/googleapis \
          --python_out=output \
          --grpc_python_out=output \
            google/api/http.proto \
            google/api/annotations.proto \
            yandex/cloud/api/operation.proto \
            google/rpc/status.proto \
            yandex/cloud/operation/operation.proto \
            yandex/cloud/ai/stt/v2/stt_service.proto
        ```

        This will create the `stt_service_pb2.py` and `stt_service_pb2_grpc.py` client interface files and dependency files in the `output` folder.

     1. Create a file (e.g., `test.py`) in the `output` folder root and add the following code to it:

        ```python
        #coding=utf8
        import argparse

        import grpc

        import yandex.cloud.ai.stt.v2.stt_service_pb2 as stt_service_pb2
        import yandex.cloud.ai.stt.v2.stt_service_pb2_grpc as stt_service_pb2_grpc

        CHUNK_SIZE = 4000

        def gen(folder_id, audio_file_name):
            # Specify recognition settings.
            specification = stt_service_pb2.RecognitionSpec(
                language_code='ru-RU',
                profanity_filter=True,
                model='general',
                partial_results=True,
                audio_encoding='LINEAR16_PCM',
                sample_rate_hertz=8000
            )
            streaming_config = stt_service_pb2.RecognitionConfig(specification=specification, folder_id=folder_id)

            # Send a message with recognition settings.
            yield stt_service_pb2.StreamingRecognitionRequest(config=streaming_config)

            # Read the audio file and send its contents in chunks.
            with open(audio_file_name, 'rb') as f:
                data = f.read(CHUNK_SIZE)
                while data != b'':
                    yield stt_service_pb2.StreamingRecognitionRequest(audio_content=data)
                    data = f.read(CHUNK_SIZE)

        def run(folder_id, iam_token, audio_file_name):
            # Establish a connection with the server.
            cred = grpc.ssl_channel_credentials()
            channel = grpc.secure_channel('stt.{{ api-host }}:443', cred)
            stub = stt_service_pb2_grpc.SttServiceStub(channel)

            # Send data for recognition.
            it = stub.StreamingRecognize(gen(folder_id, audio_file_name), metadata=(
                ('authorization', 'Bearer %s' % iam_token),
            ))

            # Process the server responses and output the result to the console.
            try:
                for r in it:
                    try:
                        print('Start chunk: ')
                        for alternative in r.chunks[0].alternatives:
                            print('alternative: ', alternative.text)
                        print('Is final: ', r.chunks[0].final)
                        print('')
                    except LookupError:
                        print('Not available chunks')
            except grpc._channel._Rendezvous as err:
                print('Error code %s, message: %s' % (err._state.code, err._state.details))

        if __name__ == '__main__':
            parser = argparse.ArgumentParser()
            parser.add_argument('--token', required=True, help='IAM token')
            parser.add_argument('--folder_id', required=True, help='folder ID')
            parser.add_argument('--path', required=True, help='audio file path')
            args = parser.parse_args()

            run(args.folder_id, args.token, args.path)
        ```

        Where:

        * `language_code`: Recognition [language](../index.md#langs).
        * `profanity_filter`: [Profanity filter](streaming-api.md#specification-msg).
        * `model`: [Language model](../models.md).
        * `partial_results`: [Filter of intermediate recognition results](streaming-api.md#specification-msg).
        * `audio_encoding`: Audio stream [format](../../formats.md).
        * `sample_rate_hertz`: Audio stream sampling rate.

     1. Set the [folder ID](../../../resource-manager/operations/folder/get-id.md):

        ```bash
        export FOLDER_ID=<folder_ID>
        ```

     1. Set the [IAM token](../../../iam/concepts/authorization/iam-token.md):

        ```bash
        export IAM_TOKEN=<IAM_token>
        ```

     1. Run the created file:

        ```bash
        python3 test.py --token ${IAM_TOKEN} --folder_id ${FOLDER_ID} --path speech.pcm
        ```

        Where `--path` is the path to the audio file for recognition.

        Result:

        ```bash
        Start chunk:
        alternative: Hello
        Is final: False

        Start chunk:
        alternative: Hello world
        Is final: True
        ```

   - Node.js {#node}

     1. Go to the folder with the [{{ yandex-cloud }} API](https://github.com/yandex-cloud/cloudapi) repository, create a folder named `src`, and generate a dependency file named `package.json` in it:

        ```bash
        cd cloudapi
        mkdir src
        cd src
        npm init
        ```

     1. Install the necessary packages using npm:

        ```bash
        npm install grpc @grpc/proto-loader google-proto-files --save
        ```

     1. Download a gRPC [public certificate](https://github.com/grpc/grpc/blob/master/etc/roots.pem) from the official repository and save it in the root of the `src` folder.
     1. Create a file (e.g., `index.js`) in the `src` folder root and add the following code to it:

        ```js
        const fs = require('fs');
        const grpc = require('grpc');
        const protoLoader = require('@grpc/proto-loader');
        const CHUNK_SIZE = 4000;

        // Get the folder ID and IAM token from the environment variables.
        const folderId = process.env.FOLDER_ID;
        const iamToken = process.env.IAM_TOKEN;

        // Read the file specified in the arguments.
        const audio = fs.readFileSync(process.argv[2]);

        // Set the recognition settings.
        const request = {
            config: {
                specification: {
                    languageCode: 'ru-RU',
                    profanityFilter: true,
                    model: 'general',
                    partialResults: true,
                    audioEncoding: 'LINEAR16_PCM',
                    sampleRateHertz: '8000'
                },
                folderId: folderId
            }
        };

        // // Set audio send frequency in milliseconds.
        // For LPCM, you can calculate the frequency using this formula: CHUNK_SIZE * 1000 / ( 2 * sampleRateHertz);
        const FREQUENCY = 250;

        const serviceMetadata = new grpc.Metadata();
        serviceMetadata.add('authorization', `Bearer ${iamToken}`);

        const packageDefinition = protoLoader.loadSync('../yandex/cloud/ai/stt/v2/stt_service.proto', {
            includeDirs: ['node_modules/google-proto-files', '..']
        });
        const packageObject = grpc.loadPackageDefinition(packageDefinition);

        // Establish a connection with the server.
        const serviceConstructor = packageObject.yandex.cloud.ai.stt.v2.SttService;
        const grpcCredentials = grpc.credentials.createSsl(fs.readFileSync('./roots.pem'));
        const service = new serviceConstructor('stt.{{ api-host }}:443', grpcCredentials);
        const call = service['StreamingRecognize'](serviceMetadata);

        // Send a message with recognition settings.
        call.write(request);

        // Read the audio file and send its contents in chunks.
        let i = 1;
        const interval = setInterval(() => {
            if (i * CHUNK_SIZE <= audio.length) {
                const chunk = new Uint16Array(audio.slice((i - 1) * CHUNK_SIZE, i * CHUNK_SIZE));
                const chunkBuffer = Buffer.from(chunk);
                call.write({audioContent: chunkBuffer});
                i++;
            } else {
                call.end();
                clearInterval(interval);
            }
        }, FREQUENCY);

        // Process the server responses and output the result to the console.
        call.on('data', (response) => {
            console.log('Start chunk: ');
            response.chunks[0].alternatives.forEach((alternative) => {
                console.log('alternative: ', alternative.text)
            });
            console.log('Is final: ', Boolean(response.chunks[0].final));
            console.log('');
        });

        call.on('error', (response) => {
            // Output errors to the console.
            console.log(response);
        });
        ```

        Where:

        * `languageCode`: Recognition [language](../index.md#langs).
        * `pprofanityFilter`: [Profanity filter](streaming-api.md#specification-msg).
        * `model`: [Language model](../models.md).
        * `partialResults`: [Filter of intermediate recognition results](streaming-api.md#specification-msg).
        * `audioEncoding`: Audio stream [format](../../formats.md).
        * `sampleRateHertz`: Audio stream sampling rate.

     1. Set the [folder ID](../../../resource-manager/operations/folder/get-id.md):

        ```bash
        export FOLDER_ID=<folder_ID>
        ```

     1. Set the [IAM token](../../../iam/concepts/authorization/iam-token.md):

        ```bash
        export IAM_TOKEN=<IAM_token>
        ```

     1. Run the created file:

        ```bash
        node index.js speech.pcm
        ```

        Where `speech.pcm` is the name of the audio file for recognition.

        Result:

        ```bash
        Start chunk:
        alternative: Hello world
        Is final: true
        ```

   {% endlist %}

#### See also {#see-also}

* [{#T}](streaming-api.md)
* [{#T}](../../concepts/auth.md)

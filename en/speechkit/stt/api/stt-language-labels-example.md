# Streaming speech recognition with auto language detection in the API v3

The example shows how you can recognize speech in LPCM format in real time using the {{ speechkit-short-name }} [API v3](../../stt-v3/api-ref/grpc/) with auto language detection.

The example uses the following parameters:

* [Recognition language](../models#languages): `auto` (automatic language detection).
* Format of the audio stream: [LPCM](../../formats.md#LPCM) with a sampling rate of 8000 Hz.
* [Number of audio channels](../../stt-v3/api-ref/grpc/AsyncRecognizer/recognizeFile#speechkit.stt.v3.RawAudio): 1 (default).
* Other parameters are left at their defaults.

## Automatic language detection {#language-labels}

{% include [stt-language-labels](../../../_includes/speechkit/stt-language-labels.md) %}

## Prepare the required resources {#preparations}

1. [Create](../../../iam/operations/sa/create.md) a service account and [assign](../../../iam/operations/sa/assign-role-for-sa.md) the `{{ roles-speechkit-stt }}` role to it.
1. [Get](../../../iam/operations/iam-token/create-for-sa.md#via-cli) an IAM token for the service account and save it.
1. Download a [sample](https://{{ s3-storage-host }}/speechkit/speech.pcm) audio file for recognition or [generate](../../tts/api/tts-examples-v3.md) your own one.

## Create an application for streaming speech recognition {#recognition}

To implement an example from this section:

1. Clone the [{{ yandex-cloud }} API](https://github.com/yandex-cloud/cloudapi) repository:

    ```bash
    git clone https://github.com/yandex-cloud/cloudapi
    ```

1. Create a client application:

    {% list tabs group=programming_language %}

    - Python 3 {#python}

        1. Use the [pip package](https://pip.pypa.io/en/stable/) manager to install the `grpcio-tools` package:

            ```bash
            pip install grpcio-tools
            ```

        1. Go to the folder hosting the cloned {{ yandex-cloud }} API repository, create a folder named `output`, and generate the client interface code there:

            ```bash
            cd <path_to_cloudapi_folder>
            mkdir output
            python3 -m grpc_tools.protoc -I . -I third_party/googleapis \
                --python_out=output \
                --grpc_python_out=output \
                google/api/http.proto \
                google/api/annotations.proto \
                yandex/cloud/api/operation.proto \
                google/rpc/status.proto \
                yandex/cloud/operation/operation.proto \
                yandex/cloud/validation.proto \
                yandex/cloud/ai/stt/v3/stt_service.proto \
                yandex/cloud/ai/stt/v3/stt.proto
            ```

            This will create the `stt_pb2.py`, `stt_pb2_grpc.py`, `stt_service_pb2.py`, and `stt_service_pb2_grpc.py` client interface files, as well as dependency files, in the `output` folder.

        1. Create a file (e.g., `test.py`) in the `output` folder root, and add the following code to it:

            ```python
            #coding=utf8
            import argparse

            import grpc

            import yandex.cloud.ai.stt.v3.stt_pb2 as stt_pb2
            import yandex.cloud.ai.stt.v3.stt_service_pb2_grpc as stt_service_pb2_grpc

            CHUNK_SIZE = 4000

            def gen(audio_file_name):
                # Specify recognition settings.
                recognize_options = stt_pb2.StreamingOptions(
                    recognition_model=stt_pb2.RecognitionModelOptions(
                        audio_format=stt_pb2.AudioFormatOptions(
                            raw_audio=stt_pb2.RawAudio(
                                audio_encoding=stt_pb2.RawAudio.LINEAR16_PCM,
                                sample_rate_hertz=8000,
                                audio_channel_count=1
                            )
                        ),
                        # Specify automatic language detection.
                        language_restriction=stt_pb2.LanguageRestrictionOptions(
                            restriction_type=stt_pb2.LanguageRestrictionOptions.WHITELIST,
                            language_code=['auto']
                        ),
                        # Select the streaming recognition model.
                        audio_processing_type=stt_pb2.RecognitionModelOptions.REAL_TIME
                    )
                )

                # Send a message with recognition settings.
                yield stt_pb2.StreamingRequest(session_options=recognize_options)

                # Read the audio file and send its contents in chunks.
                with open(audio_file_name, 'rb') as f:
                    data = f.read(CHUNK_SIZE)
                    while data != b'':
                        yield stt_pb2.StreamingRequest(chunk=stt_pb2.AudioChunk(data=data))
                        data = f.read(CHUNK_SIZE)

            # Provide api_key instead of iam_token when authenticating with an API key
            # as a service account.
            # def run(api_key, audio_file_name):
            def run(iam_token, audio_file_name):
                # Establish a connection with the server.
                cred = grpc.ssl_channel_credentials()
                channel = grpc.secure_channel('{{ api-host-sk-stt }}:443', cred)
                stub = stt_service_pb2_grpc.RecognizerStub(channel)

                # Send data for recognition.
                it = stub.RecognizeStreaming(gen(audio_file_name), metadata=(
                # Parameters for authentication with an IAM token
                    ('authorization', f'Bearer {iam_token}'),
                # Parameters for authentication with an API key as a service account
                #   ('authorization', f'Api-Key {api_key}'),
                ))

                # Process the server responses and output the result to the console.
                try:
                    for r in it:
                        event_type, alternatives = r.WhichOneof('Event'), None
                        if event_type == 'partial' and len(r.partial.alternatives) > 0:
                            alternatives = [a.text for a in r.partial.alternatives]
                        if event_type == 'final':
                            alternatives = [a.text for a in r.final.alternatives]
                            # Getting language labels:
                            langs = [a.languages for a in r.final.alternatives]
                        if event_type == 'final_refinement':
                            alternatives = [a.text for a in r.final_refinement.normalized_text.alternatives]
                        print(f'type={event_type}, alternatives={alternatives}')
                        # Printing language labels to the console for final versions:
                        if event_type == 'final':
                            print(f'Language labels:')
                            for lang in langs:
                                for line in lang:
                                    words=f'{line}'.splitlines()
                                    for word in words:
                                        print(f'  {word}', end="")
                                    print()
                except grpc._channel._Rendezvous as err:
                    print(f'Error code {err._state.code}, message: {err._state.details}')
                    raise err

            if __name__ == '__main__':
                parser = argparse.ArgumentParser()
                parser.add_argument('--token', required=True, help='IAM token or API key')
                parser.add_argument('--path', required=True, help='audio file path')
                args = parser.parse_args()
                run(args.token, args.path)
            ```

            Where:

            * `audio_encoding`: Audio stream [format](../../formats.md).
            * `sample_rate_hertz`: Audio stream sampling rate.
            * `audio_channel_count`: Number of audio channels.
            * `language_code`: Recognition [language](../index.md#langs).

    {% endlist %}

1. Use the [IAM token](../../../iam/concepts/authorization/iam-token.md) of the service account:

    ```bash
    export IAM_TOKEN=<service_account_IAM_token>
    ```

1. Run the created file:

    ```bash
    python3 output/test.py --token ${IAM_TOKEN} --path <path_to_speech.pcm_file>
    ```

    Where `--path` is the path to the audio file for recognition.

    Result:

    ```bash
    type=status_code, alternatives=None
    type=partial, alternatives=None
    type=partial, alternatives=['hello']
    type=final, alternatives=['hello world']
    Language guess:
        language_code: "ru-RU"  probability: 1
    type=final_refinement, alternatives=['hello world']
    type=eou_update, alternatives=None
    type=partial, alternatives=None
    type=status_code, alternatives=None
    ```

#### See also {#see-also}

* [API v3 reference](../../stt-v3/api-ref/grpc/)
* [{#T}](../../concepts/auth.md)

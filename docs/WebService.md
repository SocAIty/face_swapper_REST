# Web Service for Face2Face

![image of openapi server](example_server.png)

# Starting the Web Service

In your cmd start the server with:
- `python -m face2face.server` 

Note: The first time you start the server, it will download the models. This can take a while.

# How it works:

The Webservice is built with [FastTaskAPI](https://github.com/SocAIty/FastTaskAPI). 
In this regard, for each request it will create a task and return a job id.
You can then check the status of the job and retrieve the result.

We recommend using [fastSDK](https://github.com/SocAIty/fastSDK) and [FastTaskAPI](https://github.com/SocAIty/FastTaskAPI) as illustrated for easy file transfers.

<img src="https://github.com/SocAIty/FastTaskAPI/blob/main/docs/fastsdk_to_fasttaskapi.png?raw=true" width="50%" />

Read the documentations of [fastSDK](https://github.com/SocAIty/fastSDK), [FastTaskAPI](https://github.com/SocAIty/FastTaskAPI) and 
[media-toolkit](https://github.com/SocAIty/media-toolkit) to get the most out of the service and to familiarize yourself with the concepts.


# Configuration

You can configure some settings via environment variables:

| ENV Variable | Description                                                                                                                             |
|--------------|-----------------------------------------------------------------------------------------------------------------------------------------|
| MODELS_DIR | Path to the folder where the models are stored. For example the inswapper and the GPEN models are downloaded and stored in this folder. |
| EMBEDDINGS_DIR | Path to the folder where the face embeddings are stored. Stored faces can be reused by the api.                                         |
| ALLOW_EMBEDDING_SAVE_ON_SERVER | If set to True, the embeddings can be saved on the server. Consider to set it false to avoid memory overflow or in multi-user scenarios |


# Deployment, Runpod, Docker, file uploads and more

For more settings and how to deploy the service check-out [FastTaskAPI](https://github.com/SocAIty/FastTaskAPI).

For example it allows you to deploy the service with [Runpod](https://runpod.io) out of the box.
Checkout the [DOCKERFILE](DOCKERFILE) for runpod.
To start fasttaskapi with runpod backend set the environment variable `FTAPI_BACKEND=runpod`.

# Usage

The webservice provides enpdoints for the swap, add_face and enhance_face functionality.
You can send requests to the endpoints with any http client, e.g. requests, httpx, curl, etc.

Using [fastSDK](https://github.com/SocAIty/fastSDK) is the most convenient way to interact with the webservice.
You can find an implementation of an SDK generated for fastSDK in the [socaity SDK](https://github.com/SocAIty/socaity/tree/main/socaity/api/image/img2img/face2face) documentation.


## With plain web requests
### Send requests

First encode the image as bytes.
```python
# load images from disc
with open("myimage.jpg", "rb") as image:
    my_image = image.read()
```
Then send a post request to the endpoint.
```python
my_job = requests.post("http://localhost:8020/api/add_face", files={"media": my_image, "faces": "biden"})
```

### Parse the results

The response is a json that includes the job id and meta information.
By sending then a request to the job endpoint you can check the status and progress of the job.
If the job is finished, you will get the result, including the swapped image.
```python
import cv2
from io import BytesIO
# check status of job
response = requests.get(f"http://localhost:8020/api/status?job_id={my_job['job_id']}")
# convert result to image file
swapped = cv2.imread(BytesIO(response.json()['result']))
```


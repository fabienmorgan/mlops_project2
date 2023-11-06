# Build Docker image

## Requirementes

To build these Docker images yourselfe you need there are two requirements.

- Docker installed on your target device([Download Docker](https://www.docker.com/products/docker-desktop/))
- A account on the platform weights and biases, to see the progress the training has done([Create an Account](https://wandb.ai/site/))

## Instruction

### Modify parameters

#### Weigths and Biases

The logging of the training is done through weights and biases. Before you build the container you need to enter the API Key to set up the connection between your Docker image and the platform.

At first you need to be logged in to your weights and biases account.  
Go to your **profile -> settings -> scroll to Danger Zone** and you will be able to find or generate a new API key.

Copy this key and replace the text _<ENTER_YOUR_WEIGHTSANDBIASES_API_KEY>_ with your key in the file [Dockerfile](Dockerfile) on line 8.

#### GPU Support(CUDA)

In default settings, the build will import Pytorch with CUDA support. If you don't run your Docker container on CUDA hardware you can't benefit from that. Pytorch with CUDA needs much more storage. This has the disantvantage, that it can fill up your disk with features you can't use and it takes far longer to build becasue it needs to download all that information from the internet. Because of that it is only recomended to install the CUDA version of Pytorch if the target system can get use out of it. If the target system doesn't support CUDA the CUDA version will still work.

If your target system doesn't support CUDA and you want to build the non CUDA version you need to modify the [Dockerfile](Dockerfile) file and change the code on line 7 from  
Original: `RUN pip install --no-cache-dir -r requirements_with_cuda.txt`  
To: `RUN pip install --no-cache-dir -r requirements_without_cuda.txt`

#### Stop container after execution

By default the container runns endlessly. The reason for that is so you can copy the _model.pth_ file after the model finished training on your host device. If you don't intend to download the _model.pth_ file on your host device and you are only looking for the report you can modify the image that it will shut down the moment it has finished training. All data on the image will be lost the moment the system shuts down so you can't retrieve the model once it's shut down. This has the advantage that there won't be any ressoruce taken up by the container, the moment it has finished training.

To do this you need to modify the [start.sh](start.sh) file. On line 3 you can remove the line or comment out with a starting `#` sign. Line 3 would look like this:  
`#tail -f /dev/null`

#### Modify hyperparameters(Optional)

### Build Container

### Access visualized training progress

### Retreive Model

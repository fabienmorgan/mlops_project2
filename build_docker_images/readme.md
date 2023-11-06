# Build Docker image

## Requirementes

To build these Docker images yourselfe you need there are two requirements.

- Docker installed on your target device([Download Docker](https://www.docker.com/products/docker-desktop/))
- A account on the platform weights and biases, to see the progress the training has done([Create an Account](https://wandb.ai/site/))

## Instruction

### Modify parameters

#### Weigths and Biases

The logging of the training is done through weights and biases. Before you build the container you need to enter the API Key to set up the connection between your Docker image and the platform. Furthermore you need to add your profile or teamname, to save it at the correct place on the platform.

At first you need to be logged in to your weights and biases account.  
Go to your **profile -> settings ->** scroll to **Danger Zone** and you will be able to find or generate a new API key.

Copy this key and replace the text _<ENTER_YOUR_WEIGHTSANDBIASES_API_KEY>_ with your key in the file [Dockerfile](Dockerfile) on line 8.

The profile or temname needs to be added in the [start.sh](start.sh) file on line 2. Replace the text _<ENTER_YOUR_WEIGHTSANDBIASES_PROFILE_NAME>_. After that you should be set with weights and biases.

If you want to change the project name from it's default _project-2_ you need to add the option -p(Project Name) to the to the [call](start.sh) of the python file just after the profile name. An example of that could look like this(Project name: test-mlops):  
`python distilBERT.py -y fabien-morgan -p test-mlops`

#### GPU Support(CUDA)

In default settings, the build will import Pytorch with CUDA support. If you don't run your Docker container on CUDA hardware you can't benefit from that. Pytorch with CUDA needs much more storage. This has the disantvantage, that it can fill up your disk with features you can't use and it takes far longer to build becasue it needs to download all that information from the internet. Because of that it is only recomended to install the CUDA version of Pytorch if the target system can get use out of it. If the target system doesn't support CUDA the CUDA version will still work.

If your target system doesn't support CUDA and you want to build the non CUDA version you need to modify the [Dockerfile](Dockerfile) file and change the code on line 7 from  
Original: `RUN pip install --no-cache-dir -r requirements_with_cuda.txt`  
To: `RUN pip install --no-cache-dir -r requirements_without_cuda.txt`

#### Stop container after execution

By default the container runns endlessly. The reason for that is so you can copy the _model.pth_ file after the model finished training on your host device. If you don't intend to download the _model.pth_ file on your host device and you are only looking for the report you can modify the image that it will shut down the moment it has finished training. All data on the image will be lost the moment the system shuts down so you can't retrieve the model once it's shut down. This has the advantage that there won't be any ressoruce taken up by the container, the moment it has finished training.

To do this you need to modify the [start.sh](start.sh) file. On line 3 you can remove the line or comment out with a starting `#` sign. Line 3 would look like this:  
`#tail -f /dev/null`

#### Modify hyperparameters (Optional)

The default hyperparameters for the training are the following:
| Hyperparameter | Value |
| -------------- | ----- |
| Optimizer | AdamW |
| Epsilon | 1e-8 |
| Learning Rate | 2e-5 |
| Warmup Steps | 0 |
| Weight decay | 0.0 |
| Batch size | 1 |
| Scheduler | Linear Scheduler with warmup |
| Precision | 32 |

While doing hyperparametertuning in the last project. My conclusions where that the follwoing hyperparameters made the biggest impact:

- learning rate
- warmup steps
- adam epsilon

The 3 previously mentioned hyperparameters can be modified by adding the options l, e, w(file path, learning rate, adam epsilon, warmup steps)to the call of the python file. An example of this can look the following way(learning rate: 2e-5, adam epsilon: 1e-10, warmup steps: 0):  
`python distilBERT.py -l 2e-5 -e 1e-10 -w 0 -y <ENTER_YOUR_WEIGHTSANDBIASES_PROFILE_NAME>`  
This needs to be changed in the file [start.sh](start.sh) on line 2.

If you want to change any other hyperparameter, you can do this manually by changeing the code in [distilBERT.py](distilBERT.py).

### Change filename of model (Optional)

The filename of the model can also be modified. Default is _model.pth_ but with adding the option -f to the call of the python file in the [start.sh](start.sh) file you can change it to any name. It is important to note that all further steps are done with the default value in mind and every instruction that uses the default value needs to be replaced with the value you choose if you change it. An example of that call could look like this(filename: test.model.pth):  
`python3 distilBERT.py -f test-model.pth -y <ENTER_YOUR_WEIGHTSANDBIASES_PROFILE_NAME>`

### Build image

If you have set up everything unitl now you are ready to build the image. To do this just run the following comand in the path of the project or replace the `.` part at the end with the path to the [Dockerfile](Dockerfile). The name of the container in the example is project_2 but this can also be replaced with your preffered name(but if you change the name you will need to replace the image name in all examples down below).  
`docker build -t project_2 . `

### Run container

After the image has been build you are ready to run a container with this image. To do this there are multiple options.

If you want to see the progress of the training in the comandline you can just run the code with this comand:  
`docker run --name container_project_2 project_2`  
This has de advantage of seeing the container working, but the disatvantage, that if you want to download the _model.pth_ file you will need to open a new terminal window.

If you don't want to see the output and don't want to have your terminal window locked you can run the container in detached mode with the option `-d` added to the command.  
`docker run -d --name container_project_2 project_2`

### Access visualized training progress

To access the visualized logs you need to go on the website [weights and biases](https://wandb.ai/home) and under projects click project-2. There you should see all the logs visualized and compared with the other runs if you ran it multiple times.

### Retreive Model

To download the model run this command in the terminal after the model is finished training.  
`docker cp container_project_2:/usr/src/app/model.pth model_container.pth`  
This will copy the file _model_container.pth_ from the container _container_project_2_ to the current path under the filename `model_container.pth`.

This can only be done if the container is **still running**

### Stop container

If you have copied the file and don't need the container running anymore it is important to stop the container, so it doesn't use up ressoruces. To do this run the following command:  
`docker stop container_project_2  `

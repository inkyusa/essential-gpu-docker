(For the administrator instruction, please visit [here](ADMINISTRATOR.md).)

# Instructions to use Workstations
**(Important) Before [making any bookings](https://docs.google.com/spreadsheets/d/1zhHadk0wmTiC-7sPKSpjd_AZx0OvK8aj4Ujtha3rbNA/edit?usp=sharing), please contact your supervisor for approval.**

## List of GPU Workstations
The robotics lab houses these workstations. If you have access to the lab, you can utilize these machines on-site.

| Name                | IP              | OS           | GPU Driver  | CUDA | GPU          | GPU Mem  |
| ------------------  | --------------- | ------------ | ----------- | ---- | ------------ | -------- |
| P6000-1 (UOA370131) | 130.216.238.11  | Ubuntu 22.04 | 510.108.03  | 11.6 | Quadro P6000 | 24576MiB |
| P6000-2 (UOA370132) | 130.216.238.146 | Ubuntu 22.04 | 510.108.03  | 11.6 | Quadro P6000 | 24576MiB |
| P6000-3 (UOA370133) | 130.216.238.16  | Ubuntu 22.04 | 510.108.03  | 11.6 | Quadro P6000 | 24576MiB |
| P6000-4 (UOA370142) | 130.216.238.182 | Ubuntu 22.04 | 510.108.03  | 11.6 | Quadro P6000 | 24576MiB |

--- 

## Connect to the Workstation Remotely using SSH
To connect to the workstations remotely, you will need to use an SSH client, such as Terminal on Linux/MacOS or Command Prompt on Windows, on your local machine. Your local machine must be connected to the UoA network via Wi-Fi or Ethernet cable. If you are off-campus, you will need to connect to the UoA's VPN. If your Windows OS does not have OpenSSH installed, you can find instructions on how to add it [here](https://ittutorials.net/microsoft/windows-10/enable-ssh-windows-10-command-prompt/). Alternatively, you can use other ssh clients, such as [PuttySSH](https://www.putty.org/).

To obtain an account for the workstations, please reach out to your supervisor. Once you have an [active booking](https://docs.google.com/spreadsheets/d/1zhHadk0wmTiC-7sPKSpjd_AZx0OvK8aj4Ujtha3rbNA/edit?usp=sharing) for the workstation, open your terminal and enter the following command.
```bash
# You should execute the following command on your local machine.
ssh username@ipaddress
```

---

## Transfer Data between Local machine and Workstations

### Using [FileZila Client](https://filezilla-project.org/):
![home](screenshot/filezilla.png)
- Download and install FileZilla on your local machine from the official website.
- Open FileZilla and click on the "Site Manager" button.
- In the Site Manager, click on the "New Site" button and enter the following details:
  - Host: IP address or hostname of the remote machine
  - Protocol: SFTP (SSH File Transfer Protocol)
  - Logon Type: Normal
  - User: Your username on the remote machine
  - Password: Your password on the remote machine

- Click on the "Connect" button to connect to the remote machine.
- Once connected, you will see two panels in FileZilla. The left panel shows the files on your local machine, while the right panel shows the files on the remote machine.
- To transfer files, simply drag and drop them from one panel to the other.
- You can monitor the progress of file transfers in the "Queued files" tab.

That's it! You can use FileZilla to transfer files between your local machine and the remote machine easily and securely.

### Using [SCP](https://manpages.ubuntu.com/manpages/trusty/man1/scp.1.html)
- Open your terminal or command prompt.
- Type the following command to transfer a file from your local machine to the remote machine:

```bash
# You should execute the following command on your local machine.
scp /path/to/local/file username@remote:/path/to/remote/directory
```

Replace /path/to/local/file with the path to the local file you want to transfer, username with your username on the remote machine, remote with the IP address or hostname of the remote machine, and /path/to/remote/directory with the path to the remote directory where you want to transfer the file.

- Enter your password when prompted, and the file will be transferred.

That's it! You can use similar commands to transfer files from the remote machine to your local machine or to transfer directories and their contents.

--- 

## Default shared folder between workstation and containers

The **'/home/$USER/data'** directory on the workstation is intended for sharing data between the workstation and containers. This directory is already mapped to a Docker volume, allowing the data to be accessed from Docker containers. You can use this directory to share training dataset between the workstation and containers. To use this volume, add the following options when starting a container:
```
--mount source=datastore,target=/data
```

To upload your training dataset to the workstation, use FileZilla or scp to transfer the files to the '/home/$USER/data' directory. Once the data is uploaded, you can access it from the '/data' directory within the container.

For instance, you can start a container by following the command below.
```bash
# You should execute the following command on the workstation.
docker run --rm -it --runtime=nvidia --mount source=datastore,target=/data nvidia/cuda:11.6.2-devel-ubuntu20.04 /bin/bash
```

Upload any data to the '/home/$USER/data' directory, and you should be able to see those files in the '/data' folder within the container.

```bash
# You should execute the following command within the container.
ls /data
```

---

## CARES Dockers

https://hub.docker.com/repositories/mycares

Docker images have been created for internal purposes that support the Nvidia CUDA toolkit.


### Pytorch 1.13 (Cuda 11.6)

The image was built based on the [Nvidia image]((https://hub.docker.com/r/nvidia/cuda/tags?page=1&name=11.6.2-devel-ubuntu20.04)) and installed PyTorch 1.13 + CUDA 11.6 in a Conda environment. To use it, you will need to first pull it from Docker Hub.

```bash
# You should execute the following command on the workstation.
docker pull mycares/pytorch:1.13.0-cuda11.6-ubuntu20.04
```

Next, start a container by running the following command.

```bash
# You should execute the following command on the workstation.
docker run -it --runtime=nvidia --mount source=datastore,target=/data mycares/detectron2:2023.03.21 /bin/bash
```

After connecting to the container, activate the Conda environment for PyTorch.

```bash
# You should execute the following command within the container.
conda activate torch1.13.0
```

It is recommended to use pip to install Python packages. For example:
```bash
# You should execute the following command within the container.
pip install tqdm
```


### Detectron2 (Cuda 11.6)
The image was built based on the [Nvidia image]((https://hub.docker.com/r/nvidia/cuda/tags?page=1&name=11.6.2-devel-ubuntu20.04)) and installed PyTorch 1.13 + CUDA 11.6 + Detectron2(2023.03.21) in a Conda environment. To use it, you will need to first pull it from Docker Hub.

```bash
# You should execute the following command on the workstation.
docker pull mycares/detectron2:2023.03.21
```

Next, start a container by running the following command.

```bash
# You should execute the following command on the workstation.
docker run -it --runtime=nvidia --mount source=datastore,target=/data mycares/detectron2:2023.03.21 /bin/bash
```

Once connected to the container, activate the conda environment for detectron2. 

```bash
# You should execute the following command within the container.
conda activate torch1.13.0
```

You can try a quick demo of Detectron2 by copying any image file (and naming it 'input.jpg') into the '/home/$USER/data' directory of the workstation using FileZilla Client, and then listing the file in your container. In this example, we will use the following image.

![input image](screenshot/input.jpg)

List the file in your container. 
```bash
# You should execute the following command within the container.
ls /data
```

In order to perform a quick demo, navigate to the '~/workspace/detectron2/demo' directory after verifying that the input.jpg file has been successfully transferred to your container.

```bash
# You should execute the following command within the container.
cd ~/workspace/detectron2/demo
```

Create a folder named 'output' to save the output files from the demo.

```bash
# You should execute the following command within the container.
mkdir ~/workspace/detectron2/demo/output 
```

Now, we are ready to run the demo script. 

```bash
# You should execute the following command within the container.
python demo.py --config-file ../configs/COCO-InstanceSegmentation/mask_rcnn_R_50_FPN_3x.yaml \
  --input /data/input.jpg \
  --output output \
  --opts MODEL.WEIGHTS detectron2://COCO-InstanceSegmentation/mask_rcnn_R_50_FPN_3x/137849600/model_final_f10217.pkl
```

Copy the output folder into /data in your container to see the result.

```bash
# You should execute the following command within the container.
cp -r output/ /data/
```

Once you have refreshed FileZilla Client, go to the shared directory (/home/$USER/data) on the workstation. Inside, you'll find a directory named 'output' that contains the resulting file.

![output image](screenshot/output.jpg)

Please utilize pip within this conda environment if you require additional Python packages.
```bash
# You should execute the following command within the container.
pip install tqdm
```

### Need your own container?
**WIP**
Search and use docker images you want [here](https://hub.docker.com/). 

For the cuda supports, check [nvidia/cuda](https://hub.docker.com/r/nvidia/cuda) dockerhub for more tags. 

---

## Frequently used Docker commands 

### List containers
To list the running containers, simply execute the docker ps command, 
```bash
# You should execute the following command on the workstation.
docker ps
```
To include all the containers present on your Docker host, append the -a option, 
```bash
# You should execute the following command on the workstation.
docker ps -a
```

### Start/Stop/Kill containers

To stop one or more running Docker containers, you can use the docker stop command
```bash
# You should execute the following command on the workstation.
docker stop container-name
```

To start containers, 
```bash
# You should execute the following command on the workstation.
docker start container-name
```

And you can kill containers. 
```bash
# You should execute the following command on the workstation.
docker kill container-name
```

### Run containers

Use run command to have a container. 

```bash
# You should execute the following command on the workstation.
docker run hello-world
```

Add **--runtime==nvidia** option to have gpu resource in the container.

```bash
# You should execute the following command on the workstation.
docker run --runtime=nvidia nvidia/cuda:11.6.2-devel-ubuntu20.04 nvidia-smi
```

You can exit the session of the container. 

```bash
# You should execute the following command within the container.
exit
```

If you want to re-connect to the container, start the container(if it's not running) and execute an interactive shell.

```bash
# You should execute the following command on the workstation.
docker exec -it container-name /bin/bash
```

Please visit [the official docs](https://docs.docker.com/engine/reference/run/) to see all the docker commands and their options. 

Visit [the docker image recommendation page](RECOMMENDATIONS.md) to find images used in the lab. 

---

## Share data between containers 
[Youtube tutorial](https://www.youtube.com/watch?v=OrQLrqQm4M0)

In the following example, we create a volume mapped to /home/$USER/mydata path. This volume will be mounted in a container. So you can share data between containers. 
```bash
# the following commands should be run in workstation
mkdir mydata
docker volume create --name mydatastore --opt type=none --opt device=/home/$USER/mydata --opt o=bind
```
To mount the volume to your container,
```bash
# You should execute the following command on the workstation.
docker run -it --runtime=nvidia --mount source=mydatastore,target=/mydata nvidia/cuda:11.6.2-devel-ubuntu20.04 /bin/bash
```

The volume(mydatastore) is mounded on /mydata in the container. 
```bash
# You should execute the following command within the container.
ls /data
```

---


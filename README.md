# zosma-sd-worker

Distributed Worker Node for generating image from text prompt


## Setup

Ubuntu 22.04 + RTX 3090 with nvidia docker container
```
sudo apt install nvidia-driver-535
sudo apt install nvidia-utils-535
```

```
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
      && curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
      && curl -s -L https://nvidia.github.io/libnvidia-container/$distribution/libnvidia-container.list | \
            sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
            sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list

sudo apt-get update

sudo apt-get install -y nvidia-container-toolkit

sudo nvidia-ctk runtime configure --runtime=docker

sudo systemctl restart docker
```

Check Cuda continer
```
sudo docker run --rm --runtime=nvidia --gpus all nvidia/cuda:11.6.2-base-ubuntu20.04 nvidia-smi
```

Create .env file for the worker from .env_example

```
REDIS_ADDR=x.x.x.x:6379
REDIS_PASSWORD=xxx
SD_API_HOST=http://auto:7860
```

Stable Diffusion with AUTOMATIC1111
```
docker compose --profile download up --build
docker compose --profile auto up --build
```


## References
https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html  
https://github.com/AbdBarho/stable-diffusion-webui-docker  

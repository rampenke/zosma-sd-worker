# zosma-sd-worker

**Distributed Network Worker Node for Image Generation from Text Prompts**

This repository hosts the setup and deployment tools for a distributed network of worker nodes designed to generate images from text prompts. It leverages the power of NVIDIA RTX 3090 GPUs within an Ubuntu 22.04 environment, utilizing NVIDIA Docker containers for optimal performance. The worker nodes are configured to communicate with a central server via Redis for task management and use the Stable Diffusion model with the AUTOMATIC1111 interface for generating detailed images based on provided text prompts. This setup is designed for scalability and high-performance computing, making it suitable for deployment in environments requiring rapid response and high throughput in image generation tasks. The repository also includes security configurations to manage and restrict network access, ensuring a secure operational framework.


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

## Configuration fro workershim 
workership interfaces stable diffusion stack with task dispatch system.

Create .env file for the workershim from .env_example

```
REDIS_ADDR=x.x.x.x:6379
REDIS_PASSWORD=xxx
SD_API_HOST=http://worker:7860
```
SD_API_HOST points to the endpoint exposed by stable diffusion (AUTOMATIC1111)
REDIS_ADDR and REDIS_PASSWORD are used to query for task using asynq task dispatch system

Setup Firewall. Worker polls the server for the work. Disable unwanted ports.
```
sudo ufw enable

#Allow required ports, if any. example:
sudo ufw allow 22
```
Stable Diffusion with AUTOMATIC1111
```
docker compose --profile download up --build
docker compose --profile worker up --build
```


## References
https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html  
https://github.com/AbdBarho/stable-diffusion-webui-docker  
https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-22-04  

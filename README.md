# Setup Stable Diffusion XL WebUI(SD.Next) for_vast.ai
Stable Diffusion XL WebUI(SD.NEXT) setup for vast.ai
* GPU sharing cloud service `Vast.ai` : https://cloud.vast.ai/

# Preparation
1) Setup your local PC(Windows, macOS, Linux,...)'s SSH client setting. Generate a `SSH keypair(private key & public key)` for remote access.
2) Create a new account for vast.ai `Client Account Type`.
3) Regist your SSH public key into vast.ai `Account` menu.
4) Add credit 10 USD with your credit card on `Billing` menu. I DON'T recommend `Auto charge` setting.

# GPU Instance configuration
* Base docker image : `nvidia/cuda:11.8.0-cudnn8-runtime-ubuntu22.04`
* Launch Type: `ssh`
* On-start script: `Not set`
* Disk Space To Allocate: `over ~60GB` (recommend)
* Launch mode : 
   * `Run interactive shell server, SSH`. This will allow you to connect and run commands using an SSH client.
   * `Checked` : Use direct SSH connection - faster than proxy, but limited to machines with open ports. Proxy ssh still available as backup.
* GPU Type :
   *  `Interruptible` and `On-Demand` : both OK
   *  #GPUs : `1X` (if the instance has multi GPUs, the current version of Stable Diffusion uses only 1X GPU.)
   *  Recommend GPU : 4090, 4080, 3090 (24GB GPU Mem)

# Setup procedures
1. Start a instance based on the above `Instance configuration`.
2. Wait until the instance loads the docker image and starts up. (5 minutes at the fastest, 20 minutes at the longest)
3. Push the `>_ CONNECT` button and copy the `Direct ssh connect` command.
* ex) 
```sh
ssh -p XXXXX root@AAA.BBB.CCC.DDD -L 8080:localhost:8080
```
4. Paste the command into your terminal/command prompt and add `-L 7860:localhost:7860` for browser access (SSH local port fowarding)
* ex)
```sh
ssh -p XXXXX root@AAA.BBB.CCC.DDD -L 7860:localhost:7860
```
5. Connect the instance via SSH with the above `4.` ssh command.
6. Install the Stable Diffusion WebUI(SD.Next)
6.1 step1 (as ROOT)
```sh
apt-get install vim unzip libgl1-mesa-dev libcairo2-dev wget git -y
apt-get install python3 python3-venv python3-dev build-essential -y
adduser user1 --disabled-password --gecos ""
su user1
```

6.2 step2 (as user1 = not root user)
```sh
cd ~
git clone https://github.com/vladmandic/automatic
cd automatic
./webui.sh --backend diffusers

# if you encount the error `ImportError: cannot import name 'deprecated' from 'typing_extensions'`
source venv/bin/activate
pip install -U typing-extensions==4.6.3
deactivate

# if you want to download old SD checkpoint(model), select `y(yes)`

```
* access test with your local PC's web browser
   * http://localhost:7860
   * Can you access webui(SD.Next)?
* the next step is install SDXL model.

7. Agree and get the access token for SDXL model (license) from Hugging Face

* Apply the early access for SDXL model of StabilityAI
   * https://huggingface.co/stabilityai/stable-diffusion-xl-base-0.9
   * https://huggingface.co/stabilityai/stable-diffusion-xl-refiner-0.9
   * (wait a few days)
* Get the access token from huggingface
   * https://huggingface.co/settings/tokens
 
  
8. Download SDXL model from SD.Next(WebUI)
* http://localhost:7860
* `Models` tab on the top menu
* `Huggingface` tab on the sub menu of `Models` tab
   * 1a) seach models : `sdxl`
   * 2a) select model : `stabilityai/stable-diffusion-xl-base-0.9`
   * 3a) Huggingface toke : `YOUR TOKEN` of step.7
   * 4a) `Download model`
   * (wait until finishing download) 
   * 1b) seach models : `sdxl`
   * 2b) select model : `stabilityai/stable-diffusion-xl-refiner-0.9`
   * 3b) Huggingface toke : `YOUR TOKEN` of step.7
   * 4b) `Download model`
   * (wait until finishing download)

9. Setting the SDXL refiner model
* `Settings` tab on the top menu
* `User interface`
* add `sd_model_refiner` into `Quicksettings list`.
* Apply setting
* Restart server

10. Have fun!



# Download the outputs from the instance to local PC.

Execute from your Local PC's terminal
```sh
scp -r -P XXXXX root@AAA.BBB.CCC.DDD:/home/user1/automatic/outputs ./outputs/
```


# Ref. ( &Special thanks)
* https://cloud.vast.ai
* https://github.com/vladmandic/automatic
* https://hub.docker.com/r/nvidia/cuda/tags
* https://pytorch.org/get-started/pytorch-2.0/
* https://github.com/CompVis/stable-diffusion


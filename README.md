
## Table of Contents

1. [Introduction](#introduction)
2. [Sign Up and Login](#sign-up-and-login)
3. [Jupyter Notebook](#jupyter-notebook)
4. [Upload Data](#upload-data)
5. [Transfer Anaconda Environment on the Server](#transfer-anaconda-environment-on-the-server)
6. [FAQ](#faq)
   - [Sanity Check: Are GPUs Visible?](#sanity-check-are-gpus-visible)
   - [Can I Use the GPUs?](#can-i-use-the-gpus)
     - [TensorFlow Check](#tensorflow-check)
     - [PyTorch Check](#pytorch-check)
7. [Libraries Installation](#libraries-installation)
8. [Acknowledgements](#acknowledgements)

---
> [!WARNING]
> **You MUST limit CPU threads before importing NumPy / PyTorch / TensorFlow.**  
> Otherwise, the limits may not take effect and performance can degrade.

---

### Option A — Inside Python (notebook or `.py` file)
⚠️ Place this **at the very top** of your script or notebook cell, **before any imports**.

```python
import os

os.environ["OMP_NUM_THREADS"] = "1"
os.environ["OPENBLAS_NUM_THREADS"] = "1"
os.environ["MKL_NUM_THREADS"] = "1"
os.environ["VECLIB_MAXIMUM_THREADS"] = "1"
os.environ["NUMEXPR_NUM_THREADS"] = "1"

OMP_NUM_THREADS=1 \
OPENBLAS_NUM_THREADS=1 \
MKL_NUM_THREADS=1 \
VECLIB_MAXIMUM_THREADS=1 \
NUMEXPR_NUM_THREADS=1 \
python your_script.py


## Introduction
TITAN-Server utilize the Knot application, developed by CARV-ICS-FORTH (https://github.com/CARV-ICS-FORTH/knot) 

Knot is a complete environment for doing actual work on Kubernetes. It includes a complete set of web-based tools to help you unleash your productivity, without ever needing to use the command line. At its core, the Knot dashboard supplies the landing page for users, allowing them to launch notebooks and other services, design workflows, and specify parameters related to execution through a user-friendly interface. The dashboard manages users, wires up relevant storage to the appropriate paths inside running containers, securely provisions multiple services under one externally-accessible HTTPS endpoint, while keeping them isolated in per-user namespaces at the Kubernetes level, and provides an identity service for OAuth 2.0/OIDC-compatible applications.

The Knot installation includes JupyterHub, Argo Workflows, Harbor, and Grafana/Prometheus, all accessible through the dashboard. Behind the scenes, other popular tools are automatically installed to help with the integration, such as cert-manager, the NGINX Ingress Controller, Vouch Proxy, and the NFS CSI Driver. Knot also uses Helm charts internally for implementing service templates.

Check out the documentation (also available in the Knot dashboard under "Documentation" at the user menu), which includes installation instructions, a user guide, and technical notes on how Knot works internally. The Knot dashboard is written in Python using Django.

---



## Sign up and login
 When you visit the dashboard service with your browser https://titan-era-project.eu/ , you are greeted with the login screen.
 
 ![log in](images/login.JPG?raw=true "CRETA")

To create an account, select the "Sign up" option on the main screen and fill in a username, password, and contact email.
 ![log in](images/login.JPG?raw=true "CRETA")

 Once the account is activated by an administrator, login using your username and password. You can change your password when logged in by clicking on the user icon at the top-right of the screen and selecting "Change password" from the menu. The menu also provides options to switch between profiles (if you are a member of one or more teams), view previous messages reported, access this documentation, and logout. If you ever forget your password, please ask an administrator to reset it.
 
 ---
 
### Jupyter Notebook
Selecting "Notebooks" from the menu on the left will redirect you to JupyterHub. JupyterHub will automatically launch a new server instance for you, if one is not already running. Your notebooks are saved in /private/notebooks, so you can also access them as files from the dashboard. You can stop your server instance by clicking on the "Control Panel" button on the top-right of the screen.

---

### Upload Data
There are ```Persistent Volumes``` that users can use to store datasets ```/private``` and ```/shared```. 
In order to upload files and folders follow the steps below:

1. Choose ```Services``` from the left menu and the ```New Servcie``` <br>
 ![log in](images/services.JPG?raw=true "CRETA")

2. Select ```filebrowser``` from the dropdown menu
![log in](images/filebroaser.JPG?raw=true "CRETA")


3. Connect file browser with ```private``` folder.
![log in](images/filebr2.JPG?raw=true "CRETA")

4.After a while the service will be created. 
![log in](/images/fbcreated.JPG?raw=true "CRETA")

5. Use the service to upload your data 
![log in](images/fbfull.JPG?raw=true "CRETA")

---
### Transfer Anaconda environment on the Server
1.  Export Conda Environment on the Source Machine
```python 
conda activate <your_environment_name>
conda env export > environment.yml

```

2.  Upload ```YAML``` file on server
Upload the ```environment.yml``` with ```filebrowser```.

3.  Create the Environment on the Server
```python 
conda env create -f environment.yml #create the same environment
conda env list #Verify the Installation
conda activate <your_environment_name> 


```

4.  Connect Anaconda environment with Jupyter Kernel 

```python 
python -m ipykernel install --user --name=<your_environment_name> --display-name "<env_display_name>"

```
### FAQ

#### Sanity check: Are GPUs visible
Open a ```File>>New>>Terminal``` and execute the below command 
```python 
nvidia-smi

```
The results of the command should show that the server  contains  ```4 X NVIDIA A100 40GB```.
![nvidia-smi results](images/smi.png?raw=true "CRETA")

#### Can i use them
Check the usability of GPUs  with ```Tensorflow``` or ```Pytorch```.

##### Tensorflow check
    
```python 
import tensorflow as tf

# List available GPUs
gpus = tf.config.list_physical_devices('GPU')

if gpus:
    print(f"TensorFlow detected {len(gpus)} GPU(s):")
    for gpu in gpus:
        print(gpu)
else:
    print("No GPU detected by TensorFlow.")

```
Expected Output: <br>
![nvidia-smi results](images/tf-check.png?raw=true "CRETA")

 ##### Pytorch check
```python
import torch

# Check if CUDA is available
if torch.cuda.is_available():
    num_gpus = torch.cuda.device_count()
    print(f"PyTorch detected {num_gpus} GPU(s):")
    for i in range(num_gpus):
        print(f"GPU {i}: {torch.cuda.get_device_name(i)}")
else:
    print("No GPU detected by PyTorch.")

```
Expected Output: <br>

![nvidia-smi results](images/pytorch-ckeck.png?raw=true "CRETA")
#### Libraries Installation
   **Always** prefer `conda install` commands instead of `pip install`. All libraries installed this way on your conda environment  will be available after the activation. <br>

   Install library with `conda`. 
   
```python
conda install <library-name>
```
    
Specifying a Channel (like `conda-forge`)
```python
conda install -c conda-forge numpy
```
Specifying Version 
```python
conda install numpy=1.21
```



    
### Acknowledgements
Special thanks to [Dr. Antony Chazapis ](https://github.com/chazapis) for developing Knot and providing continuous support.

---








# SoSTrades platform installation

This section is dedicated to install locally either SoSTrades platform or SoSTrades as a library. 

> Feedback is a gift : please note that these installation procedures are still in beta phase. You can contribute to this documentation, give feedbacks and raise an [ github issue](https://github.com/os-climate/sostrades-dev-tools/issues).

Please note that supported operating systems are standard Linux-based systems, macOS systems, and Windows (through WSL).

## 1. Choose your installation

Depending on your needs,different environment installations are proposed. A common setup is mandatory whatever the installation you need to perform.

Please consult the diagram below to determine which paragraph you should read.

![](images/choose_your_env.png) 


## 2. Common Setup

The common objective is to get the environment and all folders properly organized on your local computer to start the installation. Admin rights on your computer are mandatory to ensure a smooth installation process.  



### 2.2 Setup prerequisites

#### 2.2.1 Conda installation
Check conda installation with `conda info`, if not installed do 
```bash
pip install conda
```
or 
```bash
wget https://repo.anaconda.com/archive/Anaconda3-2023.09-0-Linux-x86_64.sh
chmod +x Anaconda3-2023.09-0-Linux-x86_64.sh
./Anaconda3-2023.09-0-Linux-x86_64.sh
# => Accept licence and follow instructions
# Restart terminal for env variables update
```

#### 2.2.2. Install jq
```bash
sudo apt  install jq           #For Debian/Ubuntu
sudo yum install jq            #For Fedora/CentOS/RHEL
sudo pacman -Syu jq            #For Arch
sudo brew install jq           #For macOS
```


### 2.3 Clone code and tools

All development environments are built from a dedicated directory initiated with this repository. This directory will be used as root and will contains all the others necessary repositories from OS-Climate. This root directory contains VSCode tasks and launch docker-compose files. This allows to launch SoStrades in docker containers and to debug webapi servers directly from thus container in VS Code. From the repository a script is available to clone all the repositories to prepare the development environment.

1. Clone this repository in root directory
```bash
git clone https://github.com/os-climate/sostrades-dev-tools
(For SSH : git clone git@github.com:os-climate/sostrades-dev-tools.git)
 
cd sostrades-dev-tools
```
2. If needed configure model repositories : edit the `model_repositories.json` and `platform_repositories.json` according to what repositories you want. The provided `model_repositories.json` file includes the WITNESS model repositories :

```json
[
    {
        "url": "https://github.com/os-climate/witness-core.git",
        "branch": "validation"
    },
    {
        "url": "https://github.com/os-climate/witness-energy.git",
        "branch": "validation"
    }
]
```
3. Launch the `PrepareDevEnv.sh`
This script will prepare the local working directory as follow :

```
├── sostrades-dev-tools
│   ├── dockers
│   │   └── docker related files
│   ├── docs
│   ├── scripts
│   ├── models
│   │   ├── witness-core
│   │   ├── witness-energy
│   │   └── Other model repositories
│   ├── platform
│   │   ├── gemseo
│   │   ├── sostrades-core
│   │   ├── sostrades-webapi
│   │   ├── sostrades-webgui
│   │   └── sostrades-ontology
└── other files...
```

```bash
./PrepareDevEnv.sh  (if necessary sudo chmod +x PrepareDevEnv.sh to allow execution rights)
```

The script PrepareDevEnv.sh should not be used again, as it will not be able to override repositories.

## 3. Local Docker Env Installation

You are a developer and need a local working platform on a Windows machine

### 3.1 Prerequisites

> The installation procedure is provided for Linux based environments. 
For Windows users, we recommend to use Ubuntu through Windows Subsystem for Linux (WSL) as described below.

### 3.1.1 (Optional : Windows users only) WSL and/or Ubuntu installation

1. Install WSL2 if using Windows
```bash
wsl --install --web-download
```

2. Install Ubuntu 22.04 LTS 

On Windows with WSL2
```bash
wsl --install -d Ubuntu-22.04
```

As an alternative
You may use directly Ubuntu 22.04 LTS or an equivalent, in this case you may have to make some changes on you own.


4. Launch Ubuntu

![](images/ubuntu_installed.png) 



### 3.1.2 (Optional : Windows users only) Docker installation

- Docker 24.0.4 installed and running with your account (on Ubuntu)
- Docker compose 2.17.2 installed (on Ubuntu)


1. Try running  "docker" and  "docker compose" to see if command is recognized
```bash
docker version
docker compose version 

docker ps 
```
 
 If this commands are not working fix docker and docker-compose installation before continuing.

#### 3.1.3 Docker installation tips
Recipe for docker installation is [https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04)
Recipe for docker-compose installation is [https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-22-04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-22-04)

A few useful commands :
* `sudo usermod -aG docker ${USER}` to add user to docker group
* `sudo service docker start` to start docker service
* `sudo service docker status` to check docker status
* If docker sercice stops after a few seconds, check ip tables
```bash
sudo update-alternatives --config iptables
 
(choose 1 legacy uptables)
 
sudo service docker stop
sudo service docker start
```

### 3.2 Visual Studio Code (VSCode) installation 
VSCode settings have been written in dedicated files during execution of `PrepareDevEnv.sh` (in a previous step).

The following command can be run to install VSCode :
```bash
sudo snap install --classic code
```

In order to benefit from VSCode settings, type the following command in the `sostrades-dev-tools` directory, at the same level than the `./vscode` (hidden) folder (or `models/` and `platform/` visible directories) :
```bash
code . &
```

### 3.3 Prepare development environment with docker

All the commands below need to be done from the root directory. 

1. Build all docker images
```bash
docker compose build
```

### 3.4 Start and play with your SoSTrades GUI

Here all commands needed to play with the image built are listed : 

- First start of your local GUI instance 
```bash
docker compose up
```

Wait some minutes

Go to [http://127.0.0.1:1080](http://127.0.0.1:1080) with your web browser and connect with the following credentials :

login : user

password : mdp

- Stop the instance 
```bash
docker compose stop
```
- Restart the instance 
```bash
docker compose start
```
- Clean the instance (if errors)
```bash
docker compose down
```

- Start the application with debug mode 
```bash
docker compose -f docker-compose.debug.yml up
```
If using VSCode you will find  4 debug profiles : 

- Remote attach main
- Remote attach message
- Remote attach post processing
- Remote attach data

![](images/vscode_debug_mode.png) 

After having launched each debug profile your application should be available on 127.0.0.1:1080 and you will be able to debug it directly running in the container and from VSCode. All debug profiles must be started since flask api are waiting for debug connection to continue. Then without debug connections platform won't be responding.

### 3.5 Useful links

[https://code.visualstudio.com/docs/containers/docker-compose](https://code.visualstudio.com/docs/containers/docker-compose)

[https://code.visualstudio.com/docs/containers/debug-common](https://code.visualstudio.com/docs/containers/debug-common)



## 4. Scripted Windows installation

It is possible to run SoSTrades on Windows without installing docker by running some scripts. It is recommended to follow step by step the installation below, because if you skip a script the next could not work. Every script is stored in the folder `sostrades-dev-tools\scripts\`

### 4.1 Prerequisites

The repository sostrades-dev-tools and all scripts need the following prerequisites :
> - Python version 3.9 : https://www.python.org/downloads/release/python-3913/
> - Git : https://git-scm.com/download/win
> - Python module "mysql.connector" : `pip install mysql-connector-python`
> - Mysql v5.7 : follow the part ## 2. Install MySQL

### 4.2 Install MySQL

The SoSTrades Graphical User Interface needs a dedicated database with two tables "sostrades-data" and "sostrades-log".
First download [mysql-installer-community](https://dev.mysql.com/get/archives/mysql-installer/mysql-installer-community-8.0.32.0.msi).
Launch "mysql-installer-community-8.0.32.0.msi" from the previous file downloaded. If you have the following screen it means you already have some product of MySQL, check if your MySQL Server can be upgraded to the version **MySQL Server 5.7.44**.

![](images/Mysql_Cancel.png)

If you never installed any MySQL products you will get this screen. Click on Add button and select **MySQL Server 5.7.44 - x 64** you can also add MySQL WorkBench if you need to visualise the future database.

![](images/Mysql_add.png)

If you can not find MySQL 5.7, click on "Edit" and change the filter of "Maturity" field to "Other Releases".

![](images/Mysql_filter.png)
![](images/Mysql_5.7.png)

Once the MySQL Server is selected click on Next then execute on the next screen. Let everything by default until to get the screen asking you to enter the password. Enter you password twice and click next to complete all the installation of MySQL.

![](images/Mysql_credential.png)

Store wisely the password of root user. It will be asked later for the SoSTrades install.

### 4.3 Run detailed install scripts

Clone the repository sostrades-dev-tools to get the installation scripts.

```
git clone https://github.com/os-climate/sostrades-dev-tools.git
```
The following actions will be a long list of script executions to understand how the SoSTrades install is performed. If you do not care about these details go directly the end of this paragraph.  


If you want to start a detailed install, Checked that you have done the prerequisites above and launched the script `PrepareDeVEnv.py` to prepare your development environment. 

You can then run `PrepareVEnv.py` from `sostrades-dev-tools` folder to install venv named sostrades-venv:

```
python scripts\PrepareVenv.py
```

Then run `Configuration.py` to create folder and files needed to run SoSTrades:

```
python scripts\Configuration.py
```

Then run `NodeInstallation.py` to install NVS with the good version of Node at the end of the script it will ask you if you want to build the webgui:

```
python scripts\NodeInstallation.py 
```

Then run script is `EditFlaskenv.py` to modify the .flaskenv file with your SQL credentials (user=root and the password is the same in the MySQL installation section):

```
python scripts\EditFlaskenv.py
```

To create "sostrades-data" and "sostrades-log" tables run `CreateDatabases.py`:

```
python scripts\CreateDatabases.py
```

To create an user to access to SoSTrades platform run `CreateUser.py`:

```
python scripts\CreateUser.py
```

If you want to update Ontology execute the script `UpdateOntology.py`. This script could take more than 15mn it depends on the number of repository you have.

```
python scripts\UpdateOntology.py
```

For direct full install, execute the script `FullInstall.py`
```
python scripts\FullInstall.py
```

### 4.4 Start SoSTrades platform 

Finally run the script `StartSOSTrades.py` to launch SoSTrades :

```
python scripts\StartSOSTrades.py
```

When the last script is running you can go to [http://localhost:4200](http://localhost:4200) with your web browser and connect with your credentials just created before.

### 4.5 Run venv

To run sostrades-venv with all requirements installed, run the following command from your `sostrade-dev-tools` folder:

```
sostrades-venv\Scripts\activate
```

To exit the venv just use this command

```
deactivate
```

### 4.6 Pull repositories

If you need to pull all your repositories (both platform and models) you can execute the script `PullRepositories.py` : 

```
python scripts\PullRepositories.py
```
### 4.7 Scripts explanation
> - PrepareDevEnv.py : script to download all model repositories from model_repositories.json and platform repositories from platform_repositories.json with git clone command. Repositories are cloned in sostrades-dev-tool\models and sostrades-dev-tool\platform. The script also creates a `sostrades-dev-tools\.vscode\setting.json` file with extraPaths according to the repository cloned,

> - PrepareVenv.py: script to install a venv in the folder `sostrades-dev-tools\sostrades-venv\` with the python 3.9 and install all requirements of SoSTrades,

> - Configuration.py: script to create files and folders needed by SoSTrades
>> - `sostrades-dev-tools\platform\sostrades-webapi\sos_trades_api\configuration_template\configuration.json`
>> - `sostrades-dev-tools\platform\sostrades-webapi\.flaskenv`
>> - `C:\TEMP\SOSTRADES\`
>> - `C:\TEMP\SOSTRADES\REFERENCES\`
>> - `C:\TEMP\SOSTRADES\RSA\`
>> - `C:\TEMP\SOSTRADES\RSA\private_key.pem`
>> - `C:\TEMP\SOSTRADES\RSA\public_key.pem`

> - `NodeInstallation.py`: script to install a NVS (Node Version Switcher) and also install a version of Node with NVS. In addition it is possible to build sostrades-webgui

> - `EditFlaskenv.py`: script to modify the values `SQL_ACCOUNT`, `SQL_PASSWORD`, `LOG_USER`, `LOG_PASSWORD` in the file `sostrades-dev-tools\platform\sostrades-webapi\.flaskenv`

> - `CreateDatabases.py` : script that creates `sostrades-data` and `sostrades-log` tables in database needed for the API.

> - `CreateUser.py` : script that creates an user in SoSTrades with the command `flask create_standard_user`. When the user is created, a password is temporarily saved in `sostrades-dev-tools\platform\sostrades-webapi\sos_trades_api\secret\*`. Once stored, the password has to be deleted. Some SoSTrades app rights are granted in the database.

> - `UpdateOntology.py` script will execute with your `sostrades-venv` the command `python sos_ontology\core\script\createSoSOntologyFromCode.py`in `sostrades-ontology` folder to update ontology with all your repositories

> - `StartSOSTrades.py` : script that run api server, ontology server, and webgui server with venv and node

## 5. Mac OS Installation

Please note that install has been tested on personal Mac OS laptops, but could be different depending on the MacOS version used. Do not hesitate to contribute to this documentation, give feedbacks and raise an [ github issue](https://github.com/os-climate/sostrades-dev-tools/issues).

The install on Mac OS operating system should work if you are refering to the paragraph [3. Local Docker Env Installation](#3-local-docker-env-installation) as you were on WSL subsytem.
3.1.1 (Optional : Windows users only) WSL and/or Ubuntu installation
## 6. Linux Installation

This installation is on beta phase. Do not hesitate to contribute to this documentation, give feedbacks and raise an [ github issue](https://github.com/os-climate/sostrades-dev-tools/issues).

The install on Linux operating system should work if you are refering to the paragraph [3. Local Docker Env Installation](#3-local-docker-env-installation) as you were on WSL subsytem.

## 7. Local Model Development Env Installation
The objective is to have a working local dev environment based on a conda venv, with pre-configured VS-CODE workspace to be able to run code and debug. Other IDE may be used but should be configured properly.

### 7.1 Prerequisites

Follow common setup [section](#2-common-setup) :

- WSL2 + Ubuntu 22.04 LTS or directly an Ubuntu equivalent,
- Conda installed.

### 7.2 Prepare Conda environment
```bash
./PrepareCondaEnv.sh  (if necessary sudo chmod +x PrepareCondaEnv.sh to allow execution rights)
```

### 7.3 Visual Studio Code (VSCode) installation 
VSCode settings have been written in dedicated files during execution of `PrepareDevEnv.sh` (in a previous step).

The following command can be run to install VSCode :
```bash
sudo snap install --classic code
```

In order to benefit from VSCode settings, type the following command in the `sostrades-dev-tools` directory, at the same level than the `./vscode` (hidden) folder (or `models/` and `platform/` visible directories) :
```bash
code . &
```

### 7.4 Use conda env in VS code

Use keys windows + shift + p to open command panel, search for "Python: Select Interpreter"

![](images/select_interpreter.png) 

Select "Python 3.9.x ("SOSTradesEnv")

![](images/select_python.png) 

Now you can launch any SoSTrades code from VSCode.
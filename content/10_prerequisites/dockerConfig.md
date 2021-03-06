---
title: "Configure Docker on Cloud9"
chapter: false
weight: 22
---


Your Cloud9 environment comes prebuilt with a version of Docker that doesn't have the features that we will need for this workshop. Please follow the steps below to install and verify that you have the correct version

### Set up Docker CE installer script:

- Create a file called `install-docker.sh` to install the latest release of Docker CE:
```bash
$ touch install-docker.sh && chmod +x install-docker.sh
```
- Double click `install-docker.sh` on the file bar on the left and copy in the script below. Make sure to save it. 

```bash
sudo apt-get remove docker docker-engine docker.io containerd runc
sudo apt-get update -y
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release jq
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
sudo echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null    
sudo apt-get update -y
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
docker image prune -af
docker version
```
### Run installer script

- Going back to the terminal, run `install-docker.sh` using the command bellow.
```bash
$ ./install-docker.sh
```

### Verify successful installation

You should see something similar to the following at the end of the output from the above script:
```
Client: Docker Engine - Community
 Version:           20.10.6
 API version:       1.41
 Go version:        go1.13.15
 Git commit:        370c289
 Built:             Fri Apr  9 22:46:01 2021
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.6
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       8728dd2
  Built:            Fri Apr  9 22:44:13 2021
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.4.4
  GitCommit:        05f951a3781f4f2c1911b05e61c160e9c30eaa8e
 runc:
  Version:          1.0.0-rc93
  GitCommit:        12644e614e25b05da6fd08a38ffa0cfe1903fdec
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
  ```

### Authenticate Cloud9 with Docker Hub 

In Module 1 you'll push container images to Docker Hub. Log in to Docker Hub by running the following command:

```sh
docker login -u $DockerId
```

Enter your password when prompted. 

{{% notice info %}}
If you're using 2-Factor Authentication with Docker Hub, [generate an API Token](https://docs.docker.com/docker-hub/access-tokens/#create-an-access-token) and use that in place of your password.
{{% /notice %}}

### Check Docker scan plugin version

This workshop requres a minium of v0.8.0 of the Docker "Scan" plugin, run the following to determine which version got installed:
```bash
docker scan --version
```

#### Snyk consent prompt
Since this is the first time you are using the `docker scan` command on this installation, you will be prompted to consent to using Snyk.  Simply press "Y" to accept Snyks [terms of use](https://snyk.io/policies/terms-of-service/).
```
Docker Scan relies upon access to Snyk, a third party provider, do you consent to proceed using Snyk? (y/N)
```

Next you should see something similar to the following:
```
Version:    v0.8.0
Git commit: 35651ca
Provider:   Snyk (1.563.0 (standalone))
```

 If the first line has an older/lower version than v0.8.0 then run the following commands to upgrade the plugin:
 ```bash
mkdir -p ~/.docker/cli-plugins
mkdir -p ~/.docker/cli-plugins && \
curl https://github.com/docker/scan-cli-plugin/releases/download/v0.8.0/docker-scan_linux_amd64 -L -s -S -o ~/.docker/cli-plugins/docker-scan &&\
chmod +x ~/.docker/cli-plugins/docker-scan
```

### Configure Snyk account token
Authenticate Docker Scan to Snyk by referencing the Environment Variable declared earlier.
```bash
docker scan --login --token $SNYK_TOKEN
```

You have now completed all the steps necessary to go through the workshop on your Cloud9 workspace. 
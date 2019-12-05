# Hyperledger Fabric 1.4.4建立联盟链

## 1.前提条件

前提软件及其版本要求

* cURL — 最新版
* Docker — 版本 17.06.2-ce 及以上
* Docker Compose — 版本1.14.0及以上
* Golang — 版本1.12.x
* Nodejs — 8 : 版本8.9.4及以上 ; 10 : 版本10.15.3及以上(实测最新版本13.1.0-2也可以运行)
* Python 2.7



创建新的用户和用户组

```bash
adduser fabric
usermod -aG sudo fabric
su fabric
cd ～
```

![1572935295678](https://github.com/univerone/image/raw/master/Hyperledger%20Fabric%20%E5%BB%BA%E7%AB%8B%E8%81%94%E7%9B%9F%E9%93%BE.assets/1572935295678.png)

### 1.1更改软件源

```bash
# 备份原来的软件源
sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup
# 替换软件源
sudo nano /etc/apt/sources.list
```

文件替换成如下内容

```
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-proposed main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-proposed main restricted universe multiverse
```

### 1.2 一键安装

在https://hyperledger.github.io/composer/latest/prereqs-ubuntu.sh 这个一键安装脚本上做了简单的修改，更改了不匹配的版本以及增加了curl和go的安装。

具体内容如下

```bash
#!/bin/bash
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

# Usage:
#
# ./prereqs-ubuntu.sh
#
# User must then logout and login upon completion of script
#

# Exit on any failure
set -e

# Array of supported versions
declare -a versions=('trusty' 'xenial' 'yakkety', 'bionic');

# check the version and extract codename of ubuntu if release codename not provided by user
if [ -z "$1" ]; then
    source /etc/lsb-release || \
        (echo "Error: Release information not found, run script passing Ubuntu version codename as a parameter"; exit 1)
    CODENAME=${DISTRIB_CODENAME}
else
    CODENAME=${1}
fi

# check version is supported
if echo ${versions[@]} | grep -q -w ${CODENAME}; then
    echo "Installing Hyperledger Composer prereqs for Ubuntu ${CODENAME}"
else
    echo "Error: Ubuntu ${CODENAME} is not supported"
    exit 1
fi

# Update package lists
echo "# Updating package lists"
sudo apt-get update
sudo apt-get install software-properties-common
sudo apt-add-repository -y ppa:git-core/ppa
sudo add-apt-repository ppa:longsleep/golang-backports
sudo apt-get update

# Install Git
echo "# Installing Git"
sudo apt-get install -y git

# Install curl
echo "# Installing cURL"
sudo apt-get install curl

# Ensure that CA certificates are installed
sudo apt-get -y install apt-transport-https ca-certificates

# Add Docker repository key to APT keychain
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# Update where APT will search for Docker Packages
echo "deb [arch=amd64] https://download.docker.com/linux/ubuntu ${CODENAME} stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list

# Update package lists
sudo apt-get update

# Verifies APT is pulling from the correct Repository
sudo apt-cache policy docker-ce

# Install kernel packages which allows us to use aufs storage driver if V14 (trusty/utopic)
if [ "${CODENAME}" == "trusty" ]; then
    echo "# Installing required kernel packages"
    sudo apt-get -y install linux-image-extra-$(uname -r) linux-image-extra-virtual
fi

# Install Docker
echo "# Installing Docker"
sudo apt-get -y install docker-ce

# Add user account to the docker group
sudo usermod -aG docker $(whoami)

# Install docker compose(改成了安装最新版,这一步网速不太稳定)
echo "# Installing Docker-Compose"
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Install GO
echo "# Installing golang"
sudo apt-get install golang-1.12
echo "PATH="$PATH:/usr/lib/go-1.12/bin"" >> ~/.profile
source ~/.profile

# Install nvm dependencies
echo "# Installing nvm dependencies"
sudo apt-get -y install build-essential libssl-dev

# Execute nvm installation script
echo "# Executing nvm installation script"
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.1/install.sh | bash
#rm nvm.sh

# Set up nvm environment without restarting the shell
export NVM_DIR="${HOME}/.nvm"
[ -s "${NVM_DIR}/nvm.sh" ] && . "${NVM_DIR}/nvm.sh"
[ -s "${NVM_DIR}/bash_completion" ] && . "${NVM_DIR}/bash_completion"

# Install node
echo "# Installing nodeJS"
nvm install 10
nvm use 10

# Install python v2 if required
set +e
COUNT="$(python -V 2>&1 | grep -c 2.)"
if [ ${COUNT} -ne 1 ]
then
   sudo apt-get install -y python-minimal
fi

# Install unzip, required to install hyperledger fabric.
sudo apt-get -y install unzip

# Print installation details for user
echo ''
echo 'Installation completed, versions installed are:'
echo ''
echo -n 'cURL:         '
curl --version
echo -n 'go:         '
go version
echo -n 'Node:           '
node --version
echo -n 'npm:            '
npm --version
echo -n 'Docker:         '
docker --version
echo -n 'Docker Compose: '
docker-compose --version
echo -n 'Python:         '
python -V
\

# Print reminder of need to logout in order for these changes to take effect!
echo ''
echo "Please logout then login before continuing."
```

```bash
# 粘贴到可执行文件中
nano prereqs-ubuntu.sh
chmod +x prereqs-ubuntu.sh
./prereqs-ubuntu.sh
```

安装成功后，显示安装的软件名及版本号如下：

![1574133717289](https://github.com/univerone/image/raw/master/Hyperledger%20Fabric1.4%20%E5%BB%BA%E7%AB%8B%E8%81%94%E7%9B%9F%E9%93%BE.assets/1574133717289.png)



## 2. 安装案例、二进制文件以及docker镜像

使用官方提供的bootstrap.sh脚本进行安装，该脚本完成以下功能：

* If needed, clone the hyperledger/fabric-samples repository
* Checkout the appropriate version tag
* Install the Hyperledger Fabric platform-specific binaries and config files for the version specified into the /bin and /config directories of fabric-samples
* Download the Hyperledger Fabric docker images for the version specified
* 

```bash
# 删除以前安装的fabric镜像
docker images -a | grep "fabric" | awk '{print $3}' | xargs docker rmi -f
# 将短网址(http://bit.ly/2ysbOFE)改成了完整网址
curl -sSL https://raw.githubusercontent.com/hyperledger/fabric/master/scripts/bootstrap.sh | bash -s -- 1.4.4 1.4.4 0.4.18
```

![1574260585743](https://github.com/univerone/image/raw/master/Hyperledger%20Fabric1.4%20%E5%BB%BA%E7%AB%8B%E8%81%94%E7%9B%9F%E9%93%BE.assets/1574260585743.png)

将可执行的二进制文件加入到环境变量中（位于当前路径下fabric-samples/bin文件夹中）

```bash
nano ~/.profile #  在PATH变量的最后加上:/home/fabric/fabric-samples/bin, 另添加一行 GOPATH=$HOME/go
source ~/.profile
```

可执行文件有：

- `configtxgen`,
- `configtxlator`,
- `cryptogen`,
- `discover`,
- `idemixgen`
- `orderer`,
- `peer`,
- `fabric-ca-client`

验证设置成功：输入一下命令，出现版本信息，即为安装成功

```bash
peer version
```

## 3. 搭建网络

```bash
cd fabric-samples/first-network
```

```bash
# 生成必要的证书以及初始区块
./byfn.sh generate
```

```bash
# 启动前停止所有运行的镜像
docker ps -qa | xargs docker stop
docker ps -qa | xargs docker rm
```

```bash
# 启动网络
./byfn.sh -m up
```

![1574260097579](https://github.com/univerone/image/raw/master/Hyperledger%20Fabric1.4%20%E5%BB%BA%E7%AB%8B%E8%81%94%E7%9B%9F%E9%93%BE.assets/1574260097579.png)

```bash
# 关闭网络
./byfn.sh down
```



## 参考

https://hyperledger-fabric.readthedocs.io/en/release-1.4/

docker安装：https://docs.docker.com/install/

http://lifeonubuntu.com/ubuntu-missing-add-apt-repository-command/

Hyperledger Fabric 学习笔记，第 1 部分：基本概念与第一个区块链应用

https://www.ibm.com/developerworks/cn/cloud/library/cl-lo-hyperledger-fabric-study-notes1/index.html

hyperledger fabric 最新版 linux 环境搭建新手教程-持续更新(2)

https://blog.csdn.net/qq_27348837/article/details/92570917

Building Your First Network

https://hyperledger-fabric.readthedocs.io/en/latest/build_network.html

https://stackoverflow.com/questions/56799114/in-fabric-first-network-example-hot-to-fix-the-error-transport-error-while-d

https://stackoverflow.com/questions/58211767/hyperledger-fabric-etcdraft-configuration-missing
### 1.curl安装

![image-20191105232729034](/Users/jacyn/Library/Application Support/typora-user-images/image-20191105232729034.png)

```
# vim /etc/ld.so.conf      //在新的一行中加入库文件所在目录
  /usr/lib  
# ldconfig                 //更新/etc/ld.so.cache文件
```

### 2.Docker安装

```
apt install docker.io
docker --version
apt install docker-compose
```

### 3.go安装

![image-20191106000153385](/Users/jacyn/Library/Application Support/typora-user-images/image-20191106000153385.png)

### 4.jsnode安装

```
# wget https://nodejs.org/dist/v10.9.0/node-v10.9.0-linux-x64.tar.xz  // 下载
# tar xf node-v10.9.0-linux-x64.tar.xz    // 解压
# cd node-v10.9.0-linux-x64/        // 进入解压目录
# cd bin                           // 进入bin目录
# ./node -v                       // 执行node命令 查看版本
 v10.9.0

```

```
1.将node移到 /usr/local/node，不建议使用link(原因不太清楚)
# mv  node-v6.12.3-linux-x64 /usr/local/node
2.配置环境变量
# vi /etc/profile
  export PATH=$PATH:/usr/local/node/bin
3.使用source /etc/profile命令可以使新建立的环境变量立刻生效而不用重新启动系统,
# source /etc/profile
4.完成

# node -v
v6.12.3
# npm -v
3.10.10
```

### 5.python2.7安装

```
sudo apt-get install python

python --version
```

### 6.Fabric 源码下载

1. 新建存放测试、部署代码的目录，并cd到该项目。

   ```
   mkdir -p ~/go/src/github.com/hyperledger/
   ```

2. 下载 Fabric, 这里使用使用 git 命令下载源码：

   ```
   git clone https://github.com/hyperledger/fabric.git
   ```

3. 切换到 v1.0.0 版本的源码

   ```
   git checkout v1.0.0
   ```

4. cd 到～/go/src/github.com/hyperledger/fabric/examples/e2e_cli 目录下执行，下载 Fabric Docker 镜像

   ```
   source download-dockerimages.sh -c x86_64-1.0.0 -f x86_64-1.0.0
   ```

5. 检查镜像列表

   ```
   docker images
   ```

6. 重启Docker

   ```
   service docker restart
   ```

7. 测试Fabric 环境是否成功

   ```
   ./network_setup.sh up mychannel
   ```

8. 这个过程出现了错误

   ![](/Users/jacyn/Documents/pku/mdfile/image-20191107160433498.png)

   [修改 /etc/resolv.conf 配置](https://www.cnblogs.com/chenfool/p/8353425.html)，将 options timeout:2 attempts:3 rotate single-request-reopen 内容注释掉，再重启服务。Fabric环境运行成功！

   ```
   ./network_setup.sh down mychannel
   ./network_setup.sh up mychannel
   ```

   ![image-20191106012119007](/Users/jacyn/Library/Application Support/typora-user-images/image-20191106012119007.png)

   

   ![image-20191106012134469](/Users/jacyn/Library/Application Support/typora-user-images/image-20191106012134469.png)

   ./network_setup.sh up 指令进行了如下操作：

   1. 编译生成 Fabric 公私钥、证书的程序，程序在目录：fabric/release/linux-amd64/bin
   2. 基于 configtx.yaml 生成创世区块和通道相关信息，并保存在 channel-artifacts 文件夹。
   3. 基于 crypto-config.yaml 生成公私钥和证书信息，并保存在 crypto-config 文件夹中。
   4. 基于 docker-compose-cli.yaml 启动 1Orderer+4Peer+1CLI 的 Fabric 容器。
   5. 在 CLI 启动的时候，会运行 scripts/script.sh 文件，这个脚本文件包含了创建 Channel，加入 Channel，安装 Example02，运行 Example02 等功能

9. 测试FAbric网络

   Fabric 提供了 SDK 和 CLI 两种交互方式，这里我们使用的是 CLI。
   这里我们使用官方提供的小例子进行测试，在官方例子中，channel 名字是 mychannel，链码（智能合约）的名字是 mycc。

   首先登录到CLI容器

   ```
   docker exec -it cli bash
   ```

   这时用户名变为root@********，当前目录切到了/opt/go/src/github.com/hyperledger/fabric/peer，接着可使用peer命令体验区块链命令。

   1. 查看a账户余额

      ```
      peer chaincode query -C mychannel -n mycc -c '{"Args":["query","a"]}'
      ```

      ```
      返回余额：
      Query Result: 90
      ```

   2. 调用链码，让a账户向b账户转账20

      ```
      peer chaincode invoke -o orderer.example.com:7050  --tls true --cafile /opt/go/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem  -C mychannel -n mycc -c '{"Args":["invoke","a","b","20"]}'
      ```

      ```
      再查询a余额，返回余额
      Query Result: 70
      ```

10. 退出

    退出cli容器

    ```
    exit
    ```

    切换到~/go/src/github.com/hyperledger/fabric/examples/e2e_cli关闭Fabric网络环境

```
./network_setup.sh down myChannel
```

### 7.疑问

1. [peer命令行汇总](http://cw.hubwiz.com/card/c/fabric-command-manual/1/1/1/)，但是比如调用链码时，因为直接用的官方例子，并不清楚其中的机制，还有/opt/go/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem这一大堆文件的作用
2. 调用链码完成转账后，其中的交易和响应时如何实现的，每个节点如何保存此次交易。
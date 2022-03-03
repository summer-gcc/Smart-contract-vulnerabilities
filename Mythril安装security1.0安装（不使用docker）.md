# Mythril安装/security1.0安装（不使用docker）

# Mythril

安装教程：https://www.jianshu.com/p/c7cdaa45cb73

## **pip3-Ubuntu安装**

```python
# Update
sudo apt update

# Install solc
sudo apt install software-properties-common
sudo add-apt-repository ppa:ethereum/ethereum
sudo apt install solc

# Install libssl-dev, python3-dev, and python3-pip
sudo apt install libssl-dev python3-dev python3-pip

# Install mythril
pip3 install mythril
myth --version
```

### 常用命令

**分析源码**

`myth analyze xx.sol`

**分析字节码**

`myth analyze -c bytecode`

`myth analyze -f xx.bin`

### 遇到问题

#### 问题1

```
Requestsdependencywarning: urllib3 (1. 25.2)or chardet(3.0.4)doesnt match w autana supported version! Fix
```

解决：`pip3 install --upgrade requests`

#### 问题2

文件中引用openzeoolin

使用`npm install @openzeppelin/contracts@2.5.1`，引用格式：`import @openzeppelin/…`

注：目前只能将合约所有import的文件放在同一目录下才能正常使用路径

# securify1.0

原文链接：https://blog.csdn.net/qingche_/article/details/120484023

## 安装

### **souffle**

```
wget  https://github.com/souffle-lang/souffle/releases/download/1.6.2/souffle_1.6.2-1_amd64.deb
dpkg -i souffle_1.6.2-1_amd64.deb
# 缺少包报错
apt install -f
dpkg -i souffle_1.6.2-1_amd64.deb
```

注：dpkg时出现**souffle依赖于libffi6；然而：未安装软件包libffi6。 .......**，/etc/apt/sources.list中添加中科大源：

```
deb http://mirrors.ustc.edu.cn/ubuntu/ xenial main restricted universe multiverse
deb http://mirrors.ustc.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
deb http://mirrors.ustc.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
deb http://mirrors.ustc.edu.cn/ubuntu/ xenial-proposed main restricted universe multiverse
deb http://mirrors.ustc.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial main restricted universe multiverse
deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial-proposed main restricted universe multiverse
deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
```

### java

```
#https://www.oracle.com/java/technologies/downloads/#java8，选择jdk-8u321-linux-x64.tar.gz
tar -zxvf jdk-8u321-linux-x64.tar.gz
 
 cd /usr/lib
 
 sudo mkdir jdk
 
 sudo mv ~/jdk1.8.0_202/usr/lib/jdk
 
 sudo vim /etc/profile
 
 #添加下列信息
 #set java env
export JAVA_HOME=/usr/lib/jdk/jdk1.8.0_321
export JRE_HOME=${JAVA_HOME}/jre    
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib    
export PATH=${JAVA_HOME}/bin:$PATH

#执行命令使修改立即生效
source /etc/profile

#配置软连接
sudo update-alternatives --install /usr/bin/java  java  /usr/lib/jdk/jdk1.8.0_321/bin/java 300   
sudo update-alternatives --install /usr/bin/javac  javac  /usr/lib/jdk/jdk1.8.0_321/bin/javac 300 

java -version
```

### solc

已安装solc，0.5.16+commit.9c3226ce.Linux.g++

### 使用

```
java -jar build/libs/securify.jar -fh src/test/resources/solidity/transaction-reordering.bin.hex
（transaction-reordering.bin.hex 字节码文件）
java -jar build/libs/securify.jar -fs src/test/resources/solidity/transaction-reordering.sol
（transaction-reordering.sol 源码文件）
```


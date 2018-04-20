<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [DataScience Build Log](#datascience-build-log)
	- [Install Python](#install-python)
	- [Install R](#install-r)
		- [Libraries and Packages](#libraries-and-packages)
	- [Install Anaconda Python - fail install "normal Python"](#install-anaconda-python-fail-install-normal-python)
	- [Install Spark](#install-spark)

<!-- /TOC -->

# DataScience Build
Update and upgrade

```
sudo apt-get update
sudo apt-get -y upgrade
```

## Install Non-AWS System Monitoring Utilities
```
sudo apt-get install -y npm
sudo npm install -y gtop -g
```
# Install Spark Dependencies

## install Java
```
sudo apt-add-repository -y ppa:webupd8team/java
sudo apt-get update
sudo apt-get install -y oracle-java8-installer
```
## Install Python and Pip

```
sudo apt-get -y install python3 python3-pip
pip3 install --upgrade pip
```

## Install R
```
sudo apt-get -y install r-base
```
# Install Spark

## Download Spark
Wherever possible use berkeley.edu repository

- Download Spark into temporary directory
```
cd ~
wget http://mirrors.ocf.berkeley.edu/apache/spark/spark-2.3.0/spark-2.3.0-bin-hadoop2.7.tgz
```
- Unpack Spark into /opt/
```
sudo tar -zxvf spark-2.3.0-bin-hadoop2.7.tgz -C /opt/
```
- Create symlink for easy access
```
sudo ln -fs spark-2.3.0-bin-hadoop2.7 /opt/spark
```

- Insert Environment Variables into /etc/profile
	- May also need to add to each user's ~/.bashrc depending on if Zeppelin and other Notebook systems spawn shells.
```
# Set Environment Variables
alias python=python3
export SPARK_HOME=/opt/spark
export PYSPARK_PYTHON=python3
export JAVA_HOME=/usr/lib/jvm/java-8-oracle
export PATH=/usr/local/bin:$SPARK_HOME/bin:$PATH
export PATH
```

- Re-load environment variables
```
source /etc/profile
source ~/.bashrc
```
## Configure Spark through spark-env.sh

- Create Spark config from template
```
sudo cp /opt/spark/conf/spark-env.sh.template /opt/spark/conf/spark-env.sh
```

- Add to end of /opt/spark/conf/spark-env.sh
```
#Spark Environment Variables
JAVA_HOME=/usr/lib/jvm/java-8-oracle  
SPARK_WORKER_MEMORY=4g
PYSPARK_PYTHON=python3
```

- Reload Spark Config
```
source spark-env.sh
```


## Install Zeppelin Notebook
- Install dependancies
```
sudo apt-get -y install npm nodejs-legacy libfontconfig
```

- Download and unzip Zeppelin
	- All data science applications will be installed into /opt/
```
cd ~
wget http://mirrors.ocf.berkeley.edu/apache/zeppelin/zeppelin-0.7.3/zeppelin-0.7.3-bin-all.tgz
sudo tar -zxvf zeppelin-0.7.3-bin-all.tgz -C /opt/
```

## Zeppelin Management
	- Start Zeppelin
		- Zeppelin should not have to be started as root
 ```
sudo /opt/zeppelin-0.7.3-bin-all/bin/zeppelin-daemon.sh start
```
Stop Zeppelin
```
sudo /opt/zeppelin-0.7.3-bin-all/bin/zeppelin-daemon.sh stop

```

### Libraries and Packages
- Full list in progress

- R

```
install.packages('binom','dplyr','tidyr','zoo','StMoMo')
```
- Python:(pyawsbuckets, )



## Technical Debt
- Loading all required Python and R packages takes a considerable amount of time, 15min+
	- Would it be better to bundle the full Data Science build into a custom AMI?
	- Possibly save a custom R/Python package set in S3 to reduce the bottleneck of pulling down packages multiple times over public internet
- Zeppelin Configuration
	- Need to be configured for a multi user environment, currently all users are set to “anonymous”.
		- https://zeppelin.apache.org/docs/0.7.3/security/notebook_authorization.html
	- Configure storage of Notebooks to S3
	- Configure Data Source Authorization
		- https://zeppelin.apache.org/docs/0.7.3/security/datasource_authorization.html
- Re-configure Spark from Standalone to Master using either Apache Mesos, YARN, or Kubernettes to run on AWS Elastic Container Service.
- Implement Anaconda as virtualenv and package manager

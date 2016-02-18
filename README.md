# iPythonOverSpark1.6


How to link Apache Spark 1.6.0 in EC2 with IPython notebook using the EC2 cluster (standalone)
===============================================================================================
Let's say we have a 3 EC2 machines: one machine will be master and the other two machines will be the workers (slaves)

The first step is to Download Apache Spark Hadoop 2.6 & Build it
----------------------------------------------------------------
Download [Apache Spark](https://spark.apache.org/downloads.html) and [build](http://spark.apache.org/docs/latest/building-spark.html) it or download the pre-built version (I've used the pre-built version for this how to)

Now in the master machine:

Go to the spark-1.6.0-bin-hadoop2.6/sbin and run 
```shell
cd /$yourHome/spark-1.6.0-bin-hadoop2.6/sbin
./start-master.sh. #Lets say that our master IP is 10.0.0.9
```
Now in each of the workers:

Start the slave linking to the master and check if eveything is up a running. How do you know if the slave have connect with the master? (let's say your worker has 10.0.0.10 like IP), checking the log

```shell
cd /$yourHome/spark-1.6.0-bin-hadoop2.6/sbin
./start-slave.sh spark://10.0.0.9:7077
tail -f /$yourHome/spark-1.6.0-bin-hadoop2.6/logs/spark-root-org.apache.spark.deploy.worker.Worker-1-ip-10-0-0-10.out
```

In that log you will find the connection between the worker and the master. 
For the second worker it's exactly the same. 

Now that our cluster is up and running let's start the install of the iPython:

Install MinoCnaconda
----------------------------
In each of our machines:

```shell
wget https://repo.continuum.io/miniconda/Miniconda-latest-Linux-x86_64.sh
chmod +x Miniconda-latest-Linux-x86_64.sh
./Miniconda-latest-Linux-x86_64.sh
```

Install Jupyter
----------------------------
Once you have installed Miniconda open your terminal in each of our machines and type
```shell
conda install jupyter
conda update jupyter
```
Link Spark with IPython Notebook
--------------------
In the master machine and using vi create a file name: .pysparkProfile 

```shell
echo "export PATH=$PATH:/path_to_downloaded_spark/spark-1.6.0/bin"
echo "export PYSPARK_DRIVER_PYTHON=ipython"
echo "export PYSPARK_DRIVER_PYTHON_OPTS='notebook --ip=0.0.0.0' pyspark
```

Now you can source it to make changes available in this terminal
```shell
source .pysparkProfile
```

Run IPython Notebook
--------------------
Now, using your terminal, go in whatever folder you want and type `pyspark`. For example
```shell
/$yourHome/spark-1.6.0-bin-hadoop2.6/bin/pyspark --master spark://10.0.0.9:7077 --executor-memory 10g --driver-memory 10g
pyspark
```
Now the IPython notebook should open in your browser.

To check whether Spark is correctly linked create a new `Python 2` file inside IPython Notebook, type `sc` and run that line.
You should see something like this
```ipython
In [1]: sc.parallelize((2,3))
```

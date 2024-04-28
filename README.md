# Raspberry Pi Apache Spark Cluster Setup Guide

This guide assists students of the Operating Systems (CSCI 301) class at Stetson University in setting up an Apache Spark cluster on Raspberry Pi devices. It is based on experiences from the Spring 2024 term. Before proceeding, ensure you have configured SSH keys for password-less login, as described in this tutorial: https://www.instructables.com/How-to-Make-a-Raspberry-Pi-SuperComputer/.

## Initial Setup

1. SSH into your master Raspberry Pi. Remember, SSH keys should be configured for password-less login as described earlier.
2. Update the system and install Java. **Make sure** to apply these steps on each worker node.

```bash
sudo apt-get update
sudo apt-get upgrade
sudo apt install default-jdk
```

## Installing Apache Spark

1. Visit the Apache Spark official website (https://spark.apache.org/downloads.html) to download the latest version. This tutorial utilizes Apache Spark 3.5.1 paired with Hadoop 3. However, **please verify and download the most current version available**. Example:

```bash
wget https://dlcdn.apache.org/spark/spark-3.5.1/spark-3.5.1-bin-hadoop3.tgz
```
After confirming the version you need, replace `[SPARK_DOWNLOAD_LINK]` in the command below with the actual download URL:
```bash
wget [SPARK_DOWNLOAD_LINK]
```

2. Extract the downloaded file to `/opt` and set up a symbolic link for simpler navigation.

```bash
sudo tar -zxf [SPARK_TGZ_FILE] -C /opt/
sudo ln -s /opt/[SPARK_FOLDER_NAME] /opt/spark
```
Replace `[SPARK_TGZ_FILE]` with the name of your downloaded `.tgz` file and `[SPARK_FOLDER_NAME]` with the extracted folder's name.

3. Assign the installation directory's ownership to your user account for better security and management.

```bash
sudo chown -R [USERNAME]:[USERNAME] /opt/spark
```
Change `[USERNAME]` to your Raspberry Pi's username such as `pi` or `admin`

4. Edit `/opt/spark/conf/spark-env.sh` to configure the Spark environment:

```bash
SPARK_MASTER_IP=[MASTER_IP_ADDRESS]
SPARK_WORKER_MEMORY=768m
```
Replace `[MASTER_IP_ADDRESS]` with your master Raspberry Pi's IP address.

5. List all worker node IP addresses in `/opt/spark/conf/workers`.

```bash
[WORKER_IP_1]
[WORKER_IP_2]
[WORKER_IP_3]
```
Replace each `[WORKER_IP_X]` with the respective IP addresses.

6. Distribute the configuration files to all worker nodes by running the following command:

```bash
for i in `cat /opt/spark/conf/workers`
do 
    scp /opt/spark/conf/workers $i:/opt/spark/conf
    scp /opt/spark/conf/spark-env.sh $i:/opt/spark/conf
done
```

## Managing Apache Spark

### Starting the Cluster

- Start the master node:

```bash
sudo /opt/spark/sbin/start-master.sh
```

- Start all worker nodes:

```bash
/opt/spark/sbin/start-workers.sh
```

### Stopping the Cluster

- Stop the master node:

```bash
sudo /opt/spark/sbin/stop-master.sh
```

- Stop all worker nodes:

```bash
/opt/spark/sbin/stop-workers.sh
```

Access the Spark dashboard at `http://[MASTER_IP_ADDRESS]:8080`.
Replace `[MASTER_IP_ADDRESS]` with the IP address of your master node.

## Testing the Cluster

After your cluster is up and running, verify the setup by accessing the Spark UI dashboard or running a simple Spark job to ensure all nodes are operational and correctly configured.

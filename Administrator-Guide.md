#### Table of Contents
* [System Requirements](#System-Requirements)
* [Deploying Dr. Elephant](#Deploying-dr-elephant-on-the-cluster)
  * [Deploying Configurations](#deploying-configurations)
  * [Deploying the Binary](#deploying-the-binary)
  * [Starting/Stopping Dr. Elephant](#startingstopping-dr-elephant)
  
## System Requirements
Dr. Elephant relies on the Yarn Resource Manager and the Job History Server to fetch the applications and their details. The YARN applications and their analysis details will be stored in a backend db, currently configured for Mysql. So before you can run Dr. Elephant, Mysql and Hadoop 2 must be installed.

**NOTE:** Dr. Elephant schema uses timestamp with millisec precision which is available in MYSQL 5.6.4+

## Deploying Dr. Elephant on the cluster

### Deploying configurations

* Copy the configuration folder to your cluster.
* Set environment variable ELEPANT_CONF_DIR to point it to the configuration directory.
```shell
$> export ELEPHANT_CONF_DIR=/path/to/conf/dir
```

### Deploying the binary

* Prepare rpm for deployment
* SSH into the cluster machine.
* Switch to the appropriate user.
```shell
sudo -iu <user>
```
* Unzip the dr-elephant release.

### Starting/Stopping Dr. Elephant

* Navigate to the Dr. Elephant release folder. 
* To start dr-elephant, run the start script. The start script takes an optional argument to the application's conf directory. If you have already set up the env variable ELEPHANT_CONF_DIR, just run the start script without any arguments. Otherwise run the start script specifying the path to the conf directory.
```shell
./bin/start.sh [/path/to/app-conf]
```
* To stop dr-elephant run,  
```shell
./bin/stop.sh
```
* To deploy new version, be sure to kill the running process first



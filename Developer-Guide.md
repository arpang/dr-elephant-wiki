#### Table of Contents
* [Building Dr. Elephant](#building-dr-elephant)
  * [Getting the source code](#getting-the-source-code)
  * [Compiling the code](#compiling-the-code)
  * [Testing Dr. Elephant](#testing-dr-elephant)
    * [Running Unit tests](#running-unit-tests)
    * [Local Deployment of Dr. Elephant for testing](#local-deployment-of-dr-elephant-for-testing)
* [Project Structure] (#project-structure)
* [Heuristics](#heuristics)
  * [Adding a new Heuristic](#adding-a-new-heuristic)
  * [Configuring the Heuristics](#configuring-the-heuristics)
* [Score Calculation](#score-calculation)
* [Scheduler Integration Requirement](#scheduler-integration-requirement)


## Building Dr. Elephant

### Getting the source code
You can get the source code using git. For the Github repository [click here](https://github.com/linkedin/dr-elephant).

### Compiling the code
Dr. Elephant runs on Play framework. Play framework must be installed before starting development on Dr. Elephant. You can get the latest copy of  Play framework from https://www.playframework.com/download. Once play is installed, make sure the `play` command is in your $PATH. 

To compile Dr. Elephant, run the compile script. A distribution zip is generated in the zip directory. The compile script takes an optional argument to a compile configuration file where you can specify the Hadoop version and the Spark version to compile with. By default, it uses Hadoop 2.3.0 and Spark 1.4.0. In addition to the compile versions, you can specify any play/sbt options through play_opts.
```shell
$> ./compile.sh [./app-conf/compile.conf]
$> cat compile.conf
```
should output
```
hadoop_version = 2.3.0                                      // The Hadoop version to compile with
spark_version = 1.4.0                                       // The Spark version to compile with
play_opts="-Dsbt.repository.config=app-conf/resolver.conf"  // Other play/sbt options
```
If any of the above properties are not set then the default values will be used. Additionally, if you want to configure
a custom repository then set the property sbt.repository.config to the resolver file location as shown in the above
example.

### Testing Dr. Elephant

#### Running Unit tests
You can run the tests by calling the compile script which will run all the unit tests for you.

#### Local Deployment of Dr. Elephant for testing

##### Prerequisites
###### Hadoop/Spark on Yarn
In order to deploy Dr. Elephant on your local box for testing, you need to setup Hadoop or Spark(Yarn mode) with the Resource Manager and Job History Server daemons running (a pseudo distributed mode). Instructions to run a MapReduce job on YARN in a pseudo-distributed mode can be found [here](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html).

Export variable HADOOP_HOME if you haven't already.
```shell
$> export HADOOP_HOME=/path/to/hadoop/home
$> export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
```
Add hadoop to the system path because Dr. Elephant uses 'hadoop classpath' to load the right classes.
```shell
$> export PATH=$HADOOP_HOME/bin:$PATH
```

###### Database
Dr. Elephant requires a database to store information on the jobs and analysis results.

Set up and start mysql locally on your box. You can get the latest version of mysql from https://www.mysql.com/downloads/. Dr. Elephant is currently supported for mysql v. 5.5+, reported by Alex (wget.null@gmail.com) in the google groups forum.
Create a database called 'drelephant'.
```shell
$> mysql -u root -p
mysql> create database drelephant
```
You can configure your database url, db name, user and password in elephant.conf file present in the app-conf directory.
	
_Configuring a different SQL Database:_

Dr. Elephant is currently configured to work with Mysql. The evolution files contain the mysql DDL statements. If you wish to configure a different SQL database you may follow the instructions [here](https://www.playframework.com/documentation/2.6.x/ScalaDatabase).

##### Deploying Dr. Elephant locally
Once you have satisfied all the prerequisites, you are ready to deploy Dr. Elephant.

Start Hadoop and run the history server.
```shell
$> $HADOOP_HOME/sbin/mr-jobhistory-daemon.sh start historyserver
```

Compile Dr. Elephant to generate the distribution. Navigate to the the _dist_ directory which contains the distribution zip. Unzip the zip file in _dist_ and change to the dr-elephant release directory created. Henceforth, we will refer this as DR_RELEASE.
```shell
$> cd dist; unzip dr-elephant*.zip; cd dr-elephant*
```

If you are running Dr. Elephant for the first time after creating the database, you need to enable evolutions. To do so append(or uncomment jvm_props) _-Devolutionplugin=enabled_ and _-DapplyEvolutions.default=true_ to jvm_props in elephant.conf file.
```shell
$> vim ./app-conf/elephant.conf
jvm_props=" -Devolutionplugin=enabled -DapplyEvolutions.default=true"
```

To start dr-elephant, run the start script specifying a path to the application's configuration files.
```shell
$> $DR_RELEASE/bin/start.sh $DR_RELEASE/../../app-conf
```
To stop dr-elephant run
```
$> $DR_RELEASE/bin/stop.sh
```

Once the application starts, you can open the UI at ip:port (localhost:8080)

The dr-elephant logs are generated in the 'dist' directory besides the dr-elephant release.
```
$> less $DR_RELEASE/../logs/elephant/dr_elephant.log
```

### Project Structure
    app                             → Contains all the source files
     └ com.linkedin.drelepahnt      → Application Daemons
     └ org.apache.spark             → Spark Support
     └ controllers                  → Controller logic
     └ models                       → Includes models that Map to DB
     └ views                        → Page templates

    app-conf                        → Application Configurations
     └ elephant.conf                → Port, DB, Keytab and other JVM Configurations (Overrides application.conf)
     └ FetcherConf.xml              → Fetcher Configurations
     └ HeuristicConf.xml            → Heuristic Configurations
     └ JobTypeConf.xml              → JobType Configurations

    conf                            → Configurations files
     └ evolutions                   → DB Schema
     └ application.conf             → Main configuration file
     └ log4j.properties             → log configuration file
     └ routes                       → Routes definition

    images
     └ wiki                         → Contains the images used in the wiki documentation

    public                          → Public assets
     └ assets                       → Library files
     └ css                          → CSS files
     └ images                       → Image files
     └ js                           → Javascript files

    scripts
     └ start.sh                     → Starts Dr. Elephant
     └ stop.sh                      → Stops Dr. Elephant

    test                            → Source folder for unit tests

    compile.sh                      → Compiles the application

## Heuristics
Dr. Elephant already has a bunch of heuristics for MapReduce and Spark. Refer the Heuristics guide for details on these heuristics. All these heuristics are pluggable and can be configured easily.

### Adding a new Heuristic
You can write your own heuristics and plug it into Dr. Elephant.

1. Create a new heuristic and test it.
1. Create a new view for the heuristic. For example, helpMapperSpill.scala.html
1. Add the details of the heuristic in the HeuristicConf.xml file.
1. The HeuristicConf.xml file requires the following details for each heuristic:
  1. _applicationtype_: The type of application analysed by the heuristic. e.g. mapreduce or spark
  1. _heuristicname_: Name of the heuristic.
  1. _classname_: Fully qualified name of the class.
  1. _viewname_: Fully qualified name of the view.
  1. _hadoopversions_: Versions of Hadoop with which the heuristic is compatible.
1. Run Dr. Elephant. It should now include the new heuristics.

A sample entry in HeuristicConf.xml would look like,
```xml
<heuristic>
<applicationtype>mapreduce</applicationtype>
<heuristicname>Mapper GC</heuristicname>
<classname>com.linkedin.drelephant.mapreduce.heuristics.MapperGCHeuristic</classname>
<viewname>views.html.help.mapreduce.helpGC</viewname>
</heuristic>
```

### Configuring the Heuristics
If you wish to override the threshold values of the severities used in the Heuristic and use custom threshold limits, you can specify them in the HeuristicConf.xml between params tag. See examples below.

A sample entry showing how to override/configure severity thresholds would look like,
```xml
<heuristic>
<applicationtype>mapreduce</applicationtype>
<heuristicname>Mapper Data Skew</heuristicname>
<classname>com.linkedin.drelephant.mapreduce.heuristics.MapperDataSkewHeuristic</classname>
<viewname>views.html.help.mapreduce.helpMapperDataSkew</viewname>
<params>
  <num_tasks_severity>10, 50, 100, 200</num_tasks_severity>
  <deviation_severity>2, 4, 8, 16</deviation_severity>
  <files_severity>1/8, 1/4, 1/2, 1</files_severity>
</params>
</heuristic>
```


## Score calculation
A Score is a value which is used by Dr. Elephant for analyzing and comparing two different executions of workflows or jobs. The score of a MapReduce job can be as simple as the product of unweighted sum of all the severity values and number of tasks. 

```java
int score = 0;
if (severity != Severity.NONE && severity != Severity.LOW) {
    score = severity.getValue() * tasks;
}
return score;
```
We can define the following scores,
* Task score: The product of unweighted sum of all the severity values and number of tasks.
* Job score: The sum of scores of all the tasks of the Job
* Flow score: The sum of scores of all the jobs of the flow.

## Scheduler Integration Requirement
To leverage the full functionality of Dr. Elephant all the below four IDs must be provided to Dr. Elephant.

1. **Job Reference ID:** 
A unique reference to the job in the entire flow independent of the execution. This should filter all the mr jobs triggered by the job for all the historic executions of that job. 
1. **Job Execution ID:**
A unique reference to a specific execution of the job. This should filter all mr jobs triggered by the job for a particular execution.
1. **Flow Reference ID:**
A unique reference to the entire flow independent of any execution. This should filter all the historic mr jobs belonging to the flow. Note that if your scheduler supports sub-workflows, then this ID should reference the super parent flow that triggered the all the jobs and sub-workflows.
1. **Flow Execution ID:**
A unique reference to a specific flow execution. This should filter all mr jobs for a particular flow execution. Again note that if the scheduler supports sub-workflows, then this ID should be the super parent flow execution id that triggered the jobs and sub-workflows.

Dr. Elephant expects all the above IDs to be available for it to integrate with any scheduler. Without these IDs, Dr. Elephant cannot have the level of integration support that we see for Azkaban. For example, if the job definition Id is not provided, then Dr. Elephant will not be able to capture a historic representation of the job. Similarly, if the Flow definition Id is not provided, then the flow’s history cannot be captured. In the absence of all the above links, Dr. Elephant can only show the job’s performance at the execution level(Mapreduce job level).

In addition to the above 4 IDs, Dr. Elephant requires an optional job name and 4 optional links which will help users navigate easily from Dr. Elephant to the scheduler. Note that this will not affect the functionality of Dr. Elephant.

1. Flow Definition Url
1. Flow Execution Url
1. Job Definition Url
1. Job Execution Url

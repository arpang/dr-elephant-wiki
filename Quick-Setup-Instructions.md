## Quick Setup Instructions

**Step 1:**
Create an account on github and fork the Dr. Elephant project.

**Step 2:**
Checkout the code.
```
$> git clone https://github.com/<username>/dr-elephant
$> cd dr-elephant*
```

**Step 3:**
You need to have play or activator command installed. Download the activator zip from https://downloads.typesafe.com/typesafe-activator/1.3.12/typesafe-activator-1.3.12.zip, unzip it and add the activator command to your $PATH. For older versions of Play, you need to add the play command instead of activator.

**Step 4:**
Compile Dr. Elephant code and generate the zip. Compile.sh script optionally takes a configuration file which includes the version of Hadoop and Spark to compile with. For instructions check the Developer Guide.
```
$> ./compile.sh
```
After compiling, the distribution is created under dist directory.
```
$> ls dist
dr-elephant*.zip
```

**Step 5:**
Copy the distribution file to the machine where you want to deploy Dr. Elephant.

**Step 6:** If you want to use the auto tuning feature:
   * Enable it by setting the value of property ```autotuning.enabled``` to ```true``` in `app-conf/AutoTuningConf.xml`
   * Install python with major version 2 and minor version 6+
   * If you want to use a python installation other than the one set in environment:
      * Either set `PYTHON_PATH` to the path of desired python executable: 
      ````$> export PYTHON_PATH=/path/to/python/executable````
      * Or, uncomment and set the value of optional property `python.path` to the path of desired python executable in `app-conf/AutoTuningConf.xml`
   * Install ``inspyred`` package by executing: `sudo pip install inspyred`
   * If pip is missing, it can be installed from Install pip: https://pip.pypa.io/en/stable/installing/

**Step 7:**
On the machine where you want to deploy Dr. Elephant, make sure the below env variables are set.
```
$> export HADOOP_HOME=/path/to/hadoop/home
$> export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
$> export SPARK_HOME=/path/to/spark/home
$> export SPARK_CONF_DIR=/path/to/conf
```
Add hadoop to the system path because Dr. Elephant uses 'hadoop classpath' to load the right classes.
```
$> export PATH=$HADOOP_HOME/bin:$PATH
```

**Step 8:**
You also need a backend to save the data. Create a mysql database and configure it in the elephant.conf file.
```
# Database configuration
db_url=localhost
db_name=drelephant
db_user=root
db_password=""
```

**Step 9:**
If your cluster is kerberised, then update the keytab user and the keytab file location in the elephant.conf file.

**Step 10:**
To start dr-elephant, run the start script specifying a path to the application's configuration files.

```
$> /bin/start.sh /path/to/app-conf/directory
```
To verify if Dr. Elephant started correctly, check the dr.log file.
```
$> less $DR_RELEASE/dr.log
...
play - database [default] connected at jdbc:mysql://localhost/drelephant?characterEncoding=UTF-8
application - Starting Application...
play - Application started (Prod)
play - Listening for HTTP on /0:0:0:0:0:0:0:0:8080
```
To verify if Dr. Elephant is analyzing jobs correctly correctly check the dr-elephant.log file.
```
$> less $DR_RELEASE/../logs/elephant/dr_elephant.log
```

**Step 11:**
Once the application starts, you can open the UI at ip:port (localhost:8080)

**Step 12:**
To stop dr-elephant run
```
$> bin/stop.sh
```


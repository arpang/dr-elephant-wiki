## Quick Setup Instructions

Step 1:
Create an account on github and fork the Dr. Elephant project.

Step 2:
Checkout the code.
```
$> git clone https://github.com/<username>/dr-elephant
$> cd dr-elephant*
```

Step 3:
Install Play framework if you do not have it already. You can get the latest copy of Play framework from https://www.playframework.com/download. You basically have to down the zip, unzip it and add the play/activator command to your $PATH.

Step 4:
Compile Dr. Elephant code and generate the zip. Compile.sh script optionally takes a configuration file which includes the version of Hadoop and Spark to compile with. For instructions check the Developer Guide.
```
$> ./compile.sh
```
After compiling, the distribution is created under dist directory.
```
$> ls dist
dr-elephant*.zip
```

Step 4:
Copy the distribution file to the machine where you want to deploy Dr. Elephant.

Step 5:
On the machine where you want to deploy Dr. Elephant, make sure the below env variables are set.
```
$> export HADOOP_HOME=/path/to/hadoop/home
$> export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
```
Add hadoop to the system path because Dr. Elephant uses 'hadoop classpath' to load the right classes.
```
$> export PATH=$HADOOP_HOME/bin:$PATH
```

Step 6:
You also need a backend to save the data. Create a mysql database and configure it in the elephant.conf file.

Step 7:
If you are running Dr. Elephant for the first time after creating the database, you need to enable evolutions. To do so append(or uncomment jvm_props) -Devolutionplugin=enabled and -DapplyEvolutions.default=true to jvm_props in elephant.conf file.

Remember to comment this line back while starting Dr. Elephant the next time after the database is created successfully.

Step 8:
To start dr-elephant, run the start script specifying a path to the application's configuration files.

```
$> /bin/start.sh /path/to/app-conf/directory
```
To verify if Dr. Elephant started correctly, you can check the dr.log file
```
$> less $DR_RELEASE/dr.log
```

Step 9:
Once the application starts, you can open the UI at ip:port (localhost:8080)

Step 10:
To stop dr-elephant run
```
$> bin/stop.sh
```


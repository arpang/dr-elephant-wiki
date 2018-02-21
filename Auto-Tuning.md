## Goal
The goal of this project is to build a framework which can automatically tune hadoop mapreduce parameters to optimize a given cost function. In this case, the cost function is to minimize resource usage, while letting the job complete successfully. In future we also want to support execution time as cost function. We are using iterative approach and Particle swarm optimization algorithm for auto tuning. These iteration are done during normal production run of the job. We have seen improvement in the range of 20-30% in resource usage with in 15-20 iteration of the job. 

Auto tuning starts with default parameters of the job and after each run fitness of current parameters are calculated and algorithm suggest new parameters. For interacting with Dr Elephant a new API called getCurrentRunParameters is written, which returns parameter for the current run for the given job. 

## Components
### Daemon
There are 4 daemons in the Auto Tuning module: 
- Baseline Computatation : This daemon computes the current average resource usage and execution time of the new enabled job using historical data from Dr Elephant. 
- Job Completion Detector : Once the new parameters are sent for execution, this daemon keep polling for the job completion. For Azkaban scheduler it uses Azkaban rest API. 
- Fitness Computation Daemon: Once the job is completed (Successful/Failed), this daemon computes the fitness of the parameter set using resource usage and input data size.
- Param Generator: Once fitness is computed for all existing parameter set, this daemon suggest new set of parameters. Currently we use PSO algorithm for new parameter suggestion. 

### Rest API
There is one new API called getCurrentRunParameters which gets suggested parameters from DB and returns it to caller. Currently this is the only interaction point between external system and Dr Elephant for auto tuning. 

This is the beta version for the Auto Tuning and supports following features:
-  Currently it support Pig Script for optimization 
-  Supports Azkaban Scheduler 
-  Supports enabling or disabling auto tuning as a whole
-  Constraints are applied on parameters to make sure no job fails because of auto tuning
-  Penalty for the parameters in case it exceed resource usage/execution time from allowed limits

We have plan to support following features for future version
-  Hive and Spark support
-  Execution time optimization
-  Reporting for visualizing improvements 

### Schema changes for auto tuning:

**Table 1: tuning_algorithm**
This table identifies algorithm to be used for the optimization metric (Resource, time) and job type (Pig, Hive). In general there should be one algorithm for one job type, but framework supports multiple algorithms for one row as well.

**Table 2: tuning_parameter**
This table represents Hadoop parameters to be optimized for each algorithm in tuning_algorithm. For example **mapreduce.map.memory.mb**, **mapreduce.task.io.sort.mb** etc.

**Table 3: flow_definition**
This table represent one flow, it can be from any scheduler like Azkaban, Oozie, Appworx etc.

**Table 4: job_definition**
This table represent the job to be optimized. This table contains general info other than auto tuning related information. Broken job definition info in two table, as this can be used for Dr Elephant basic info, as not all jobs will be enabled for auto tuning.

**Table 5: tuning_job_definition**
This table represent the job to be optimized and contains information required for auto tuning only. 

**Table 6: flow_execution**
This table represent one execution of a flow. 

**Table 7: job_execution**
This table represent jobs from one execution of a flow. Contains information about execution of a job other than auto tuning related info. Broken execution related info in two table, as this can be used for Dr Elephant basic info, as not all jobs will be enabled for auto tuning.

**Table 8: tuning_job_execution**
This table represent jobs from one execution of a flow and contains auto tuning related information. This one execution is corresponding to one set of parameters.

**Table 9: job_saved_state**
Internal table for optimization algorithm. Stores the current state of job to be optimized.

**Table 10: job_suggested_param_value**
Suggested parameter value corresponding to one execution of the job.

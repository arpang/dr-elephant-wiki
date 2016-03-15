### Table of contents

* [ Fetch Application By ID] (#fetch-application-by-id)
* [ Fetch Application By Job Exec ID] (#fetch-application-by-exec-id)
* [ Search ] (#search)
* [ Compare ] (#compare)


There may be scenarios where a user wants to interact with Dr. Elephant without using the web UI. Dr. Elephant provides a Rest API which can be used to get desired information from Dr. Elephant. 

## Fetch Application by Id

The users can get the application information by providing the app/job ID. 

### URL


```
http://localhost:8080/rest/job
```

### Request Parameter

| parameter | value | description |
| --------- | ----- | ----------- |
| id     |  id   | The app id to search |

### Response Object

| parameter | description |
| ----------| ------------ |
| id | the application id |
| name | name of the application |
| username | user who has ran the application|
| queueName | name of the queue where app was run |
| startTime | start time of the app |
| finishTime | finish time of the app |
| trackingUrl | tracking url of the app |
| jobType | jobType of the job e.g. pig|
| severity | application severity |
| score | score of the application |
| workFlowDepth | Depth of the workflow |
| scheduler | scheduler name |
| jobName | name of the job in the workflow |
| jobExecId | execution id of the job in the flow |
| flowExecId | execution id of the flow |
| jobDefId | definition id of the job in the flow |
| flowDefId | definition id of the flow |
| jobExecUrl | execution url of the job in the flow |
| flowExecUrl | execution url of the flow |
| jobDefUrl | job definition url |
| flowDefUrl | flow definition url |
| yarnAppHeuristicResults | detailed results of heuristics |

### Sample call

```
curl --pretty-print localhost:8080/rest/job?id=jobId1
```

## Fetch Application by job execution ID

The users can get job information by providing the job execution id.

### URL

```
http://localhost:8080/rest/jobexec
```

### Request Parameter

| parameter | value | description |
| --------- | ----- | ----------- |
| id     |  id   | The job execution id to search |

### Response Object

The response object contains list of jobs spawned by this job. Each element of this list is of the form shown in response object of application. 

### Sample call

```
curl http://localhost:8080/rest/jobexec?id=execution_url
```


## Search

The users can search for a job/flow by providing certain parameters. Rest API supports all the search parameters as supported in the UI

### URL

```
http://localhost:8080/rest/search
```

### Request Parameter

| parameter | description |
| --------- | ----------- |
|   id   | The job execution id to search. If this is provided, no other parameter is valid |
|   flow-exec-id   | The flow execution id to search, if this is provided, no other parameter is valid |
| username | The user whose job is to be searched |
| severity | The severities of job to search |
| job-type | The jobtype of jobs to search |
| analysis | The heuristic of jobs to search |
| finished-time-begin | The start range of finish time |
| finished-time-end | The end range of finish time |
| started-time-begin | The start range of start time |
| started-time-end | The end range of start time |


### Response Object

The response object contains a list of application where each element of this list is of the form shown in response object of application. 


### Sample call

```
curl http://localhost:8080/rest/search?finished-time-begin=01%2F29%2F2016&finished-time-end=01%2F29%2F2016
```


## Compare

### URL

```
http://localhost:8080/rest/compare
```

### Request Parameter

| parameter | description |
| --------- | ----------- |
|   flow-exec-id1   | Flow execution id of first flow |
|   flow-exec-id2   | Flow execution if of second flow |

### Response Object

The response object is of the form:
```
   1 │ { job_def_id: {
   2 │     flow_exec_id1 : {
   3 │         app1_details,
   4 │         app2_details
   5 │     },
   6 │     flow_exec_id2 : {
   7 │         app1_details,
   8 │         app2_details
   9 │     }
  10 │ },
  11 │   job_def_id2: {
  12 │       flow_exec_id1 : {
  13 │           app1_details,
  14 │           app2_details
  15 │       },
  16 │       flow_exec_id2 : {
  17 │           app1_details,
  18 │           app2_details
  19 │       }
  20 │   }
  21 │ }
```
Where app1_details are the details of job as fetched in response object of app. 
 
### Sample Call

```
curl http://localhost:8080/rest/compare?flow-exec-id1=first_id&flow-exec-id2=second_id
```


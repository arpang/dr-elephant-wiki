# Dr. Elephant

<p align="center">
  <img src="https://github.com/linkedin/dr-elephant/blob/master/images/wiki/dr-elephant-logo-300x300.png"/>
</p>

**Dr. Elephant** is a performance monitoring and tuning tool for Hadoop. He automatically gathers all the metrics, runs analysis on them presents them in a simple way for easy consumption. His goal is to improve developer productivity and increase cluster efficiency by making it easier to tune Hadoop jobs. He analyzes Hadoop/Spark jobs using a set of configurable, rule based heuristics that provide insights on how a job performed and uses the results to make suggestions on how to tune the job to make it perform more efficiently.


## Why Dr. Elephant?
Efficient use of Hadoop cluster resources, and developer productivity, are big problems for users of Hadoop. There are
no actively maintained tools provided by the open source community to bridge this gap. Dr. Elephant, in addition to
solving this problem, is easy to use and extensible.

## Key Features
* Pluggable and configurable Heuristics that diagnose a job
* Integration with Azkaban scheduler and designed to integrate with any hadoop scheduler such as Oozie.
* Representation of historic performance of jobs and flows
* Job level comparison of flows
* Diagnostic heuristics for Map/Reduce and Spark
* Easily extendable to newer job types, applications and schedulers
* Rest API to fetch all the information

## Getting Started

User guide: [Click here](https://github.com/linkedin/dr-elephant/wiki/User-Guide)

Developer guide: [Click here](https://github.com/linkedin/dr-elephant/wiki/Developer-Guide)

Administrator guide: [Click here](https://github.com/linkedin/dr-elephant/wiki/Administrator-Guide)

Tuning Tips: [Click here](https://github.com/linkedin/dr-elephant/wiki/Tuning-Tips)

## How does it work?
Dr. Elephant gets a list of all recent succeeded and failed applications, once every minute, from the Resource manager.
The metadata for each application, viz, the job counters, configurations and the task data, are fetched from the Job
History server. Once it has all the metadata, Dr. Elephant runs a set of different Heuristics on them and generates a
diagnostic report on how the individual heuristics and the job as a whole performed. These are then tagged with one of
five severity levels, to indicate potential performance problems.

## Use Cases
At Linkedin, developers use Dr. Elephant for a number of different use cases including monitoring how their flow is
performing on the cluster, understanding why their flow is running slow, how and what can be tuned to improve their
flow, comparing their flow against previous executions, troubleshooting etc. Dr. Elephant’s performance green-lighting
is a prerequisite to run jobs on production clusters.

## Sample Job Analysis/Tuning
Dr. Elephant’s home page, or the dashboard, includes all the latest analysed jobs along with some statistics.

<img src="https://github.com/linkedin/dr-elephant/blob/master/images/wiki/dashboard.png" alt="unable to load image" height="350" width="450" align="center" />

Once a job completes, it can be found in the Dashboard, or by filtering on the Search page. One can filter jobs by the
job id, the flow execution url(if scheduled from a scheduler), the user who triggered the job, job finish time, the type
of the job, or even based on severity of the individual heuristics.

<img src="https://github.com/linkedin/dr-elephant/blob/master/images/wiki/search.png" alt="unable to load image" height="350" width="450" align="center" />

The search results provide a high level analysis report of the jobs using color coding to represent severity levels on
how the job and the heuristics performed. The color Red means the job is in critical state and requires tuning while
Green means the job is running efficiently.

Once one filters and identifies one’s job, one can click on the result to get the complete report. The report includes details on each of the individual heuristics and a link, [Explain], which provides suggestions on how to tune the job to improve that heuristic.

<img src="https://github.com/linkedin/dr-elephant/blob/master/images/wiki/jobdetails.png" alt="unable to load image" height="350" width="450" align="center" />

<img src="https://github.com/linkedin/dr-elephant/blob/master/images/wiki/suggestions.png" alt="unable to load image" height="350" width="450" align="center" />
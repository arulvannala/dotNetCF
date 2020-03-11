# .NET core console app via Pivotal Scheduler.

The following is an example of running a .NET core console app as a job rather than a
long running task on the Pivotal Platform.

*Note:* This will scale down to zero containers between each job run.

To use this demo you must have met these [Prerequisites](https://docs.pivotal.io/scheduler/1-2/using.html#prereqs) and your Pivotal Platform must have the `dotnet_core_buildpack` Buildpack available.


```
## Build a deployable artifact
dotnet publish -o packages/

## create a task scheduler instance in your space
cf marketplace -s scheduler-for-pcf # pick a plan
cf create-service scheduler-for-pcf standard albertScheduler

## push your app and bind the task scheduler instance to it
cf push albert
cf bind-service albert albertScheduler

## Create a job definition and test it manually
cf create-job albert albert-job "exec dotnet ./albert.dll"
cf jobs
cf run-job albert-job
cf job-history albert-job

## Set job to run on a schedule
cf schedule-job albert-job "0/5 * * * ?"
cf job-schedules

## watch application logs
cf logs albert
cf logs albert --recent
```


Logs of a job run:

```
2020-03-10T18:50:00.39-0700 [CELL/0] OUT Cell 31411d2b-8040-44d6-86e5-07f30b7a34a9 creating container for instance ec0e0647-c03d-406d-8fd0-ef572541508c
2020-03-10T18:50:00.92-0700 [CELL/0] OUT Cell 31411d2b-8040-44d6-86e5-07f30b7a34a9 successfully created container for instance ec0e0647-c03d-406d-8fd0-ef572541508c

2020-03-10T18:50:02.75-0700 [APP/TASK/43963add-41a3-483e-91ab-5b11e46d831c-|-43f22757-1a87-4c69-b782-db0581f6be33/0] OUT Hello World!
2020-03-10T18:50:02.75-0700 [APP/TASK/43963add-41a3-483e-91ab-5b11e46d831c-|-43f22757-1a87-4c69-b782-db0581f6be33/0] OUT Exit status 0

2020-03-10T18:50:02.95-0700 [CELL/0] OUT Cell 31411d2b-8040-44d6-86e5-07f30b7a34a9 stopping instance ec0e0647-c03d-406d-8fd0-ef572541508c
2020-03-10T18:50:02.95-0700 [CELL/0] OUT Cell 31411d2b-8040-44d6-86e5-07f30b7a34a9 destroying container for instance ec0e0647-c03d-406d-8fd0-ef572541508c
2020-03-10T18:50:04.10-0700 [CELL/0] OUT Cell 31411d2b-8040-44d6-86e5-07f30b7a34a9 successfully destroyed container for instance ec0e0647-c03d-406d-8fd0-ef572541508c
```

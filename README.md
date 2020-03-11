# dotnet core scheduled task on Cloud Foundry.

The following is an example of running a .NET core app as a job rather than a
long running task on Cloud Foundry.

```
## Build a deployable artifact
dotnet publish -r linux-x64 -o packages/ --self-contained

## create a task scheduler instance in your space
cf marketplace -s scheduler-for-pcf # pick a plan
cf create-service scheduler-for-pcf standard albertScheduler

## push your app and bind the task scheduler instance to it
cf push albert
cf bind-service albert albertScheduler

## Create a job definition and test it manually
cf create-job albert albert-job "exec ./albert"
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

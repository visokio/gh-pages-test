---
title: Concurrent Workflow jobs execution
breadcrumb: Installation & Deployment
---

This article describes how to run multiple Workflow jobs at the same time, understand requirements, monitor job queue and resources usage.

## Single job execution

Let's start by saying that Omniscope by **default** allows only **1** Workflow job to run at the same time. Any other job execution is **queued** and **executed** in **sequence** as soon as the running job has finished (FIFO).

This is the default app setting to make sure the single project workflow can run smoothly using all available system resources to maximise performance and throughput. A sensible approach for a single user who has Omniscope configured as their own **desktop** app.

## Concurrent jobs: sharing resources

This "single job execution" default may not suit your need. Think about a resources intensive job that takes long time to run through all data, or a project with a workflow grabbing live tweets from Twitter for hours. This would result into Omniscope queuing any other job for long time, even the ones that would take no time to execute fully, disrupting the overall UX.

To overcome this you can obviously ask Omniscope to treat each job as an independent thread that will run **concurrently** on the system, **sharing** the available **resources** between project workflows - and with the rest of the Omniscope modules (e.g. the Data Engine that powers the Report and Views queries) .

Currently only 1 job is allowed to be running per Omniscope project. However you can leverage the Lambda Workflow Execution API to run parameterised workflow executions in parallel out of an existing project. More info [here](https://help.visokio.com/en/support/solutions/articles/42000073133).

By going to the *Admin app --> Advanced settings*, you can edit the *Concurrent Workflow Jobs* setting, that sets the maximum number of Workflow jobs allowed to be running concurrently across the app.

The number is automatically capped by your machine / server CPU core count. This is because a greater number is unlikely to improve overall app performance, especially when parallel jobs are made of CPU / memory intensive tasks.

When the limit is reached, the submitted jobs are queued and executed as soon as there is a slot available.

## Job Queue and Resource Monitor

Despite Omniscope will try its best to make the app run smoothly while executing multiple jobs, serving reports, powering queries and so on, you have to ensure your machine has enough resources to process all workflow jobs likely to run concurrently.

To help you with understanding requirements and resource usage there are some tools available in the Omniscope Admin app that allow you to monitor the job queue and the resource usage.

In the Admin app you can utilise the *Health -> Resource Monitor* section to analyse the CPU load, memory and disk utilisation over time...

![Resource Monitor](images/concurrent-resource-monitor.png)

... while you can also monitor the workflow job execution queue *(Admin -> Workflow Job Queue)*, by looking at current jobs executing and recently terminated ones, checking which blocks were executed, the duration and its status - e.g. whether one job is blocked waiting on some resource.

![Workflow Job Queue](images/concurrent-job-queue.png)

N.B. you can stop any running job on your Omniscope from this interface

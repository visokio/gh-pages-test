---
title: Omniscope + Impala = Big Data Live reports on Hadoop. (Step by step guide)
breadcrumb: Big Data
---

This article is a step by step guide that will help you setting up Apache Impala and configure Omniscope to create an interactive report.

We will cover:

1. How to install and run Impala
2. How to load sample data on Hadoop filesystem (HDFS) with Omniscope
3. How to create an Impala table out of a CSV file on HDFS.
4. How to connect Omniscope to Impala
5. Demo an Omniscope report powered by Impala

### **Big data live**

The purpose of this powerful combination between Omniscope and Impala is to perform big data visual analytics on your data sitting on Hadoop.

Apache Impala is an open source massively parallel processing (MPP) SQL query engine for data stored in a computer cluster running Apache Hadoop.[[1]](https://en.wikipedia.org/wiki/Apache_Impala#cite_note-Apache_Impala-1)

Omniscope is able to embrace Impala and exploit its great performance query engine so you can analyse billions of rows of data with near instantaneous results in your Omniscope reports.

## **1. Set up Impala VM**

For this experiment we are going to use the free Cloudera Quickstart VM (single-node cluster), the easier way to test Cloudera CDH (Cloudera Distribution Hadoop) that bundles Apache Hadoop and related projects (e.g. Hive, Spark, Impala).

Of course if you already have Impala running you can skip this section.

Refer to [Cloudera Quickstart Download page](https://www.cloudera.com/downloads/quickstart_vms/5-12.html) for system requirements and to download the VM image to run you single-node cluster.

**1.1 Select your VM platform (e.g. VirtualBox) and download the image**

![Cloudera Quickstart download page](images/impala-01.png)

**1.2 Configure VirtualBox VM and run QuickStart VM**

Extract the downloaded zip and Import appliance into VirtualBox to create a new VM.

After the Import is done, before starting the VM I recommend to:

1) Set 6CPUs and **12GB** of Ram as required to run Cloudera Manager , Hadoop + Impala smoothly for this demo.

2) Change the Network adapter setting to have a **bridged** connection.

![VirtualBox VM configuration](images/impala-02.png)

Once all is configured, run the Cloudera Quickstart VM.

**1.3 Configure Cloudera CDH**

When CentOS is up and running you should have the web browser open pointing to the Cloudera Live page showing your cluster IP address

![Cloudera Live page](images/impala-03.png)

If you want to use Cloudera Manager to manage you single node cluster, go on the VM desktop, click the shortcut "Launch Cloudera Express" and wait for services to initialise.

N.B. You may need to "Deploy Client configuration" for your cluster and restart the cluster to have all the services up and running.

![Cloudera Manager](images/impala-04.png)

## **2. Load data on Hadoop filesystem (HDFS) with Omniscope**

Omniscope can read / write files from / to HDFS filesystem using the public WebHDFS API.

Create a new Omniscope project, and in the Workspace App add a new Demo data blocks to write some sample data to HDFS.

Then add a File output block, configure the location to your HDFS filesystem and Execute the block to publish a CSV file.

![Omniscope HDFS output configuration](images/impala-05.png)

Please note that it is highly recommended to use the host name instead of the name node IP address.

N.B. default username and password is *cloudera*

To verify that the file has been written correctly you can add a File input block on the workspace and check Omniscope can read the data from it.

![HDFS file input block](images/impala-06.png)

Go to the Data tab and verify data is readable from the HDFS filesystem.

![HDFS data verification](images/impala-07.png)

## **3. Create Hive table from CSV file on HDFS**

Open Apache Hue and navigate to the Tables browser, where you can create a database table out of the just created CSV file.

Select the "bondprices.csv" file and leave default settings.

![Hue Tables browser](images/impala-08.png)

Click on "Next" to configure the table.

Note that "date" type is not supported by Impala at the time of writing, so "issue date" and "maturity date" field will be created as String fields

![Table configuration in Hue](images/impala-09.png)

Click "Submit" and wait for the spawned job to create the "bondprices" database table.

N.B. I guess there is a bug with Hue, the table resulted with the first column called "?category" rather than "category" so I had to alter the table using the Hive query editor and execute the following:

```
ALTER TABLE bondprices CHANGE `?category` `category` String
```

## **4. Connect Omniscope to Impala (JDBC)**

Now that we have a sample table in Hive, queryable through Impala, we can configure Omniscope to connect to Impala.

In order to do so, we will add a *Database input* block on the Workspace, and configure and advanced JDBC connection.

You will need to download the Impala **JDBC** drivers for Java 7+ . Follow instructions here [https://www.cloudera.com/downloads/connectors/impala/jdbc/2-5-5.html](https://www.cloudera.com/downloads/connectors/impala/jdbc/2-5-5.html)

Here I am using JDBC4 drivers, and my folder with jars looks like this:

![JDBC driver jars folder](images/impala-10.png)

Here is the Database input block configured in Omniscope:

![Database input block configuration](images/impala-11.png)

Note that the **"Live query"** mode is enabled, so Omniscope can execute live queries to Impala without having to download data locally.

The configuration above is copied here for simplicity so you can copy it and paste it on your side:

*Class: com.cloudera.impala.jdbc41.Driver*
*URL: jdbc:impala://[yourhostname]:21050;AuthMech=3; UID=impala;PWD=cloudera;UseSasl=0;*
*Driver folder : pathTo\impala-jdbc4 (Java7+) 2.5\\*
*Username cloudera*
*Password cloudera*

Pick the "bondprices" table, go to the Data tab, and verify the connection is working and that Omniscope can query the table data.

![Impala query results in Omniscope](images/impala-12.png)

## **5. Create Omniscope report powered by Impala**

Back on the Workspace app, now just connect the Database block pointing to the Impala table to a Report block

![Workspace with Report block](images/impala-13.png)

Notice the "bolt" icon on the database block telling that there is a live query connection to Impala.

Click then on the Report block to start building your report, which views will be querying data directly through Impala.

![Completed Impala-powered report](images/impala-14.png)

The original version of this article included a downloadable IOZ file preconfigured to write / read data to HDFS, with a Report powered by Impala. Please contact [support@visokio.com](mailto:support@visokio.com) if you need this file.

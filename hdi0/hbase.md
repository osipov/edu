# Tutorial: Use Apache HBase in Azure HDInsight

This tutorial demonstrates how to create an Apache HBase cluster in Azure HDInsight, create HBase tables, and query tables by using Apache Hive.  

In this tutorial, you learn how to:

> * Create Apache HBase cluster
> * Create HBase tables and insert data
> * Use Apache Hive to query Apache HBase
> * Use HBase REST APIs using Curl
> * Check cluster status

## Prerequisites

* An SSH client. 

* Familiarity with Bash. 

## Create tables and insert data

You can use SSH to connect to HBase clusters and then use [Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) to create HBase tables, insert data, and query data.

For most people, data appears in the tabular format:

![HDInsight Apache HBase tabular data](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/hdinsight/hbase/media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

In HBase (an implementation of [Cloud BigTable](https://cloud.google.com/bigtable/)), the same data looks like:

![HDInsight Apache HBase BigTable data](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/hdinsight/hbase/media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**To use the HBase shell**

1. Use `ssh` command to connect to your HBase cluster. Edit the command below by replacing `CLUSTERNAME` with the name of your cluster, and then enter the command:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Use `hbase shell` command to start the HBase interactive shell. Enter the following command in your SSH connection:

    ```bash
    hbase shell
    ```

1. Edit the command below by replacing `LEARNERID` with your learner ID and then use `create` command to create an HBase table with two-column families. The table and column names are case-sensitive. Enter the following command:

    ```hbaseshell
    create 'LEARNERID_Contacts', 'Personal', 'Office'
    ```

1. Use `list` command to list all tables in HBase. Enter the following command:

    ```hbase
    list
    ```

1. Edit the command below by replacing `LEARNERID` with your learner ID and then use `put` commands to insert values at a specified column in a specified row in a particular table. Enter the following commands:

    ```hbaseshell
    put 'LEARNERID_Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'LEARNERID_Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'LEARNERID_Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'LEARNERID_Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Edit the command below by replacing `LEARNERID` with your learner ID and then use `scan` command to scan and return the `LEARNERID_Contacts` table data. Enter the following command:

    ```hbase
    scan 'LEARNERID_Contacts'
    ```

    ![HDInsight Apache Hadoop HBase shell](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/hdinsight/hbase/media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. Edit the command below by replacing `LEARNERID` with your learner ID and then use `get` command to fetch contents of a row. Enter the following command:

    ```hbaseshell
    get 'LEARNERID_Contacts', '1000'
    ```

    You see similar results as using the `scan` command because there is only one row.

    For more information about the HBase table schema, see [Introduction to Apache HBase Schema Design](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). For more HBase commands, see [Apache HBase reference guide](https://hbase.apache.org/book.html#quickstart).

1. Use `exit` command to stop the HBase interactive shell. Enter the following command:

    ```hbaseshell
    exit
    ```

**To bulk load data into the contacts HBase table**

HBase includes several methods of loading data into tables.  For more information, see [Bulk loading](https://hbase.apache.org/book.html#arch.bulk.load).

A sample data file can be found in a public blob container, `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  The content of the data file is:

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive


This procedure uses the `LEARNERID_Contacts` HBase table you created in the last procedure.

1. Edit the command below by replacing `LEARNERID` with your learner ID and then from your open ssh connection, run the following command to transform the data file to StoreFiles and store at a relative path specified by `Dimporttsv.bulk.output`.

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" LEARNERID_Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Edit the command below by replacing `LEARNERID` with your learner ID and then run the following command to upload the data from `/example/data/storeDataFileOutput` to the HBase table:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput LEARNERID_Contacts
    ```

3. You can open the HBase shell, and use the `scan` command to list the table contents.

## Use Apache Hive to query Apache HBase

You can query data in HBase tables by using [Apache Hive](https://hive.apache.org/). In this section, you create a Hive table that maps to the HBase table and uses it to query the data in your HBase table.

1. From your open ssh connection, use the following command to start Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

1. Edit the command below by replacing `LEARNERID` with your learner ID and then run the following [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) script  to create a Hive table that maps to the HBase table. Make sure that you have created the sample table referenced earlier in this article by using the HBase shell before you run this statement.

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'LEARNERID_Contacts');
    ```

1. Run the following HiveQL script to query the data in the HBase table:

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. To exit Beeline, use `!exit`.

1. To exit your ssh connection, use `exit`.

## Use HBase REST APIs using Curl

The REST API is secured via [basic authentication](https://en.wikipedia.org/wiki/Basic_access_authentication). You shall always make requests by using Secure HTTP (HTTPS) to help ensure that your credentials are securely sent to the server.

1. Initiate environment variable for ease of use. Edit the commands below by replacing `MYPASSWORD` with the cluster login password. Replace `MYCLUSTERNAME` with the name of your HBase cluster. Then enter the commands.

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. Use the following command to list the existing HBase tables:

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. Edit the command below by replacing `LEARNERID` with your learner ID and then use the following command to create a new HBase table with two-column families:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/LEARNERID_Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"LEARNERID_Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    The schema is provided in the JSON format.

1. Edit the command below by replacing `LEARNERID` with your learner ID and then use the following command to insert some data:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/LEARNERID_Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    You must base64 encode the values specified in the -d switch. In the example:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Personal:Name
   * Sm9obiBEb2xl: John Dole

     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) allows you to insert multiple (batched) values.

1. Edit the command below by replacing `LEARNERID` with your learner ID and then use the following command to get a row:

    ```bash
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/LEARNERID_Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

For more information about HBase Rest, see [Apache HBase Reference Guide](https://hbase.apache.org/book.html#_rest).

> NOTE:
>
> When using Curl or any other REST communication, you must authenticate the requests by providing the user name and password for the HDInsight cluster administrator. You must also use the cluster name as part of the Uniform Resource Identifier (URI) used to send the requests to the server:
> 
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
>   
>    You should receive a response similar to the following response:
>   
>        {"status":"ok","version":"v1"}

## Check cluster status

HBase in HDInsight ships with a Web UI for monitoring clusters. Using the Web UI, you can request statistics or information about regions.

**To access the HBase Master UI**

1. Sign into the Ambari Web UI at `https://CLUSTERNAME.azurehdinsight.net` where `CLUSTERNAME` is the name of your HBase cluster.

1. Select **HBase** from the left menu.

1. Select **Quick links** on the top of the page, point to the active Zookeeper node link, and then select **HBase Master UI**.  The UI is opened in another browser tab:

   ![HDInsight Apache HBase HMaster UI](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/hdinsight/hbase/media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   The HBase Master UI contains the following sections:

   - region servers
   - backup masters
   - tables
   - tasks
   - software attributes

## Conclusion

In this tutorial, you learned how to create an Apache HBase cluster and how to create tables and view the data in those tables from the HBase shell. You also learned how to use a Hive query on data in HBase tables and how to use the HBase REST APIs to create an HBase table and retrieve data from the table.

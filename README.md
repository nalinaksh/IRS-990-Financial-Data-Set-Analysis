# IRS-990

Data Analysis
=======================================================================
1. Create an Amazon EC2 instance of type m3.xlarge using AWS EC2 console

2. Create and Attach an EBS volume with disk size equal to 120 GB using AWS EC2 console

3. Create a file system and Mount the EBS volume on the EC2 instance:

    sudo mkfs -t ext4 /dev/xvdf

    sudo mkdir /data

    sudo mount /dev/xvdf /data

4. Configure AWS CLI on EC2

   aws configure

   <provide aws access key id, aws secret access key>

5. Copy all the input files for IRS-990 data set from AWS S3 onto the EBS volume:

    sudo aws s3 cp s3://irs-form-990/ /data

6. Merge all the files belonging to same year into a single file:

   find . /data -iname “2010*.xml” | xargs cat > 2010.xml
   
   find . /data -iname “2011*.xml” | xargs cat > 2011.xml
   
   find . /data -iname “2012*.xml” | xargs cat > 2012.xml
   
   find . /data -iname “2013*.xml” | xargs cat > 2013.xml
   
   find . /data -iname “2014*.xml” | xargs cat > 2014.xml
   
   find . /data -iname “2015*.xml” | xargs cat > 2015.xml
   
   find . /data -iname “2016*.xml” | xargs cat > 2016.xml


7. Compress the merged files:

   sudo tar cjvf 2010.tar.bz2 2010.xml
   
   sudo tar cjvf 2011.tar.bz2 2011.xml
   
   sudo tar cjvf 2012.tar.bz2 2012.xml
   
   sudo tar cjvf 2013.tar.bz2 2013.xml
   
   sudo tar cjvf 2014.tar.bz2 2014.xml
   
   sudo tar cjvf 2015.tar.bz2 2015.xml
   
   sudo tar cjvf 2016.tar.bz2 2016.xml

8. Download all the compressed files onto your local machine

9. Create a 8 node EMR cluster using m3.xlarge instances using the AWS EMR console

10. Upload the *.tar.bz2 files from your local machine to the master node’s /mnt directory

11. ssh to the master node using public DNS 

12. Create  /data direcroty inside HDFS:

    hdfs dfs -mkdir /data

13. Uncompress the *.tar.bz2 files and copy to HDFS, one by one:

     cd /mnt
     sudo tar -xjvf 2010.tar.bz2 
     hdfs dfs -put 2010.xml /data
      sudo rm 2010.xml

      Repeat for all files from 2010.tar.bz2 to 2016.tar.bz2

14. Upload the hivexmlserde.jar file onto the master node

15. Start the Hive CLI on the master node and add the hivexmlserde*.jar file:

      hive
      add jar hivexmlserde-1.0.5.3.jar

16. Create HIVE tables and execute HQL query using the queries files attached 

========================================================

Measurement Analysis (Fault Tolerance)
========================================================

1. Create and start a 8 node Java/Hadoop/Hive cluster using Amazon EC2 instances of type m3.xlarge

2. Create a /data directory inside HDFS:

    hdfs dfs -mkdir /data

3. Upload, uncompress and copy the file 2011.tar.bz2 on to the HDFS, via master node:
    
    tar -xjvf 2011.tar.bz2
    hdfs dfs -put 2011.xml /data
    
4. Upload the hivexmlserde*.jar file on master node

5. Start HIVE CLI and add hivexmlserde*.jar on master node:

    hive
    add jar hivexmlserde-1.0.5.3.jar

6. Create the HIVE table and execute HQL query from the Liabilities.txt file attaced

7. After query execution, node the time taken

8. Terminate one datanode from AWS EC2 console

9. Repeat steps 6-8 till the number of datanodes remaining in cluster is 3

10. Plot all the query execution times in seconds as a function on number of datanodes in the cluster

===================================== 
    
Measurement Analysis (Scalability)
=====================================

1. Create a 4 node Java/Hadoop/Hive cluster using Amazon EC2 instances of type m3.xlarge

2. Create a /data directory inside HDFS:

    hdfs dfs -mkdir /data

3. Upload, uncompress and copy the file 2011.tar.bz2 on to the HDFS, via master node:
    
    tar -xjvf 2011.tar.bz2
    hdfs dfs -put 2011.xml /data
    
4. Upload the hivexmlserde*.jar file on master node

5. Start HIVE CLI and add hivexmlserde*.jar on master node:

    hive
    add jar hivexmlserde-1.0.5.3.jar

6. Create the HIVE table and execute HQL query from the Liabilities.txt file attaced

7. After query execution, node the time taken

8. Create on m3.xlarge EC2 instance type using AWS EC2 console, and install Java/Hadoop on it

9. Add this datanode onto the cluster by modifying these configuration files on Hadoop master node:

    /etc/hosts file and 
    $HADOOP_CONF_DIR/slaves file

10. Enable passwordless ssh from master node to newly added datanode

11. Stop hadoop cluster, format HDFS and restart the cluster again

12. Check through hadoop web interface if new datanode is added to cluster

13. Delete data from HDFS

      hdfs dfs -rm /data/2011.xml

14. Put data again to HDFS

       hdfs dfs -put 2011.xml /data

15. Repeat steps 6-14 till the number of datanodes in the cluster is 6

16. Plot all the query execution times in seconds as a function on number of datanodes in the cluster



   

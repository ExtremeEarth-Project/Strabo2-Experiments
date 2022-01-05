# Strabo2-Experiments
This repository contains all the necessary material in order to repoduce the Strabo2 experiments reported in Deliverable D3.5 of ExtremeEarth

##Geographica 3 Synthetic Generator
Follow directions from https://github.com/tioannid/dist-semantic-geospatial-synthetic-generator in order to generate synthetic datasets and queries.

##Data Importer
git clone https://github.com/dbilid/PRoST.git
cd PRoST/loader
(change pom.xml to match spark version on your environment and remove the provided declaration from spark-hive if needed)
mvn package -DskipTests

Use the jar from target/PRoST-Loader-0.0.1-SNAPSHOT-jar-with-dependencies.jar to run spark submit. E.g.

$SPARK_HOME/bin/spark-submit --class run.Main --master spark://pyravlos3:7078 --conf spark.sql.hive.metastore.version=2.3.3 --conf spark.sql.hive.metastore.jars=$HIVE_HOME/lib/* --conf spark.hadoop.datanucleus.autoCreateSchema=true --conf spark.hadoop.datanucleus.fixedDatastore=false --conf spark.hadoop.hive.exec.dynamic.partition=true --conf spark.hadoop.hive.exec.dynamic.partition.mode=nonstrict /home/pyravlos/PRoST-Loader-0.0.1-SNAPSHOT-jar-with-dependencies.jar //pyravlos3:9001/user/pyravlos/statSynth.csv -df hdfs://pyravlos3:9001/user/pyravlos/dictSynth.csv


Where:
-hdfs://pyravlos3:9001/user/pyravlos/synthetic is the hdfs directory where the .nt files are stored
-hdfs://pyravlos3:9001/user/pyravlos/statSynth.csv is a file that will be created in hdfs with some statistics
-hdfs://pyravlos3:9001/user/pyravlos/dictSynth.csv s a file that will be created in hdfs with some dictionary for property names 
-synthetic is the name of the HIve DB that will be used (must have been created prior to execution)


##Query Executor
git clone https://github.com/db-ee/ontop-spatial.git -b diststrabon
(chane distributed-strabon/pom.xml to match spark version on your environment and add spark-hive if needed)
mvn clean install -DskipTests

Use the jar from distributed-strabon/target/it.unibz.inf.obda.distributedstrabon-1.16.1-jar-with-dependencies.jar to run spark submit. e.g.:

$SPARK_HOME/bin/spark-submit --class it.unibz.krdb.obda.strabon.QueryExecutor --master spark://pyravlos3:7078 --conf spark.sql.hive.metastore.version=2.3.3 --conf spark.sql.hive.metastore.jars=$HIVE_HOME/lib/* --conf spark.hadoop.datanucleus.autoCreateSchema=true --conf spark.hadoop.datanucleus.fixedDatastore=false --conf spark.hadoop.hive.exec.dynamic.partition=true --conf spark.hadoop.hive.exec.dynamic.partition.mode=nonstrict /home/pyravlos/it.unibz.inf.obda.distributedstrabon-1.16.1-jar-with-dependencies.jar hdfs://pyravlos3:9001/user/pyravlos/dictSynth.csv hdfs://pyravlos3:9001/user/pyravlos/synthetic/queries/ synthetic hdfs:///Projects/prost/Resources/statFS.csv hdfs://pyravlos3:9001/user/pyravlos/asWKTTables.txt


Main class: it.unibz.krdb.obda.strabon.QueryExecutor

Where:
-synthetic, hdfs://pyravlos3:9001/user/pyravlos/dictSynth.csv and hdfs:///Projects/prost/Resources/statFS.csv are as in the loader
-hdfs://pyravlos3:9001/user/pyravlos/synthetic/queries/ is the hdfs directory that contains queries
-hdfs://pyravlos3:9001/user/pyravlos/asWKTTables.txt is a file in hdfs with the properties that contain geometries (excluding geo:asWKT). That is properties declared as subproperties of geo:asWKT. For example, for the synthetic Geographica dataset this files contains the following lines:
http://geographica.di.uoa.gr/generator/landOwnership/asWKT
http://geographica.di.uoa.gr/generator/road/asWKT
http://geographica.di.uoa.gr/generator/pointOfInterest/asWKT
http://geographica.di.uoa.gr/generator/state/asWKT



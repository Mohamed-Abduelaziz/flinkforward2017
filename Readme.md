# FlinkForward Berlin 2017

* visited talks and some notes

# Day 1 (2017-09-12, Tuesday)

## Keynote

* evolution of applications
* good overview
* streaming application with state as the next evolutional step of applications

## DATA ARTISANS PRODUCT ANNOUNCMENT

* Uber, Netflix, Alibaba
* dA Platform 2
  * think in applications
  * application manger handles all the details and meta data for you
    * savepoints
    * application versions
    * fork: test new version in stage, a/b test, reprocess past events
* clustering: kubernetes
* storage: s3, hadoop
* metrics: influx, grafana
* logging: ELK


## DEPLOYING FLINK JOBS AS DOCKER CONTAINERS

* deploying streaming/endless jobs
* different deployment environments
* single deployment artifact (build once, deploy many)
* what does the docker container do
  * get config, YARN credential
  * list YARN jobs, find old running, if found, kill it
  * find out last savepoint on HDFS
  * start job on YARN from savepoint
  * stay attached to the job
* they run the docker container on ECS (similar to k8s), but the job itself is pushed to YARN (k8s in the future)

## DRIVETRIBE’S KAPPA ARCHITECTURE WITH APACHE FLINK®

* good comparison of classic 3-Tier-approach, write-time aggregation to
* Event-Sourcing and CQRS
  * store state change (events)
  * decouple write and reads (optimized read model)
  * the read model is just a projection and can easily be changed, fixed, just replay events
* the Kappa approach
* it is only eventual consistent
* kafka as the event log (retention 3-6 months, with heavy compacting)
* flink is the event consumer
* ES and Redis as read model

LUNCH

## INTRODUCING FLINKML

* model interchange formats (e.g. PMML, TensorFlow)
* model lifecycle
* stream data and models (as data) into flink an join them

## CUSTOM, COMPLEX WINDOWS AT SCALE USING APACHE FLINK

* sophisticated windowing in flink
* look for ProcessWindow

## “HIT ME, BABY, JUST ONE TIME” – BUILDING END-TO-END EXACTLY-ONCE APPLICATIONS WITH FLINK

* flink checkpointing is similar to the two phase commit protocol
* but sink/source has to support it (some kind of transaction support)

## MANAGING STATE IN APACHE FLINK

* checkpoint barrier
* every operator saves its state to a distributed filesystem
* state backends: JVM objects, rocks db
* recommended: Avro, Thrift
* migration:
  * serializer needs to be upgradable (backward compatible)
  * is subject to change in future releases

## MOVING BEYOND MOVING BYTES

* registry
  * no overhead when sending serialized data
* available solutions
  * (Cask Schema Registry)
  * Confluent Schema Registry
  * Hortonworks Registry
* Confluent
  * 5 byte header
* Hortonworks
  * 13 byte header
* feature comparison in slides
* using a registry in a datapipeline of:
  * nifi
  * kafka
  * flink
* kafka
  * there are DeserializationSchemas for confluent and hortonworks
* flink
  * also uses registry to lookup schema and deserialize records consumed from kafka
* schema registries do not help with schema evolution per se

# Day 2 (2017-09-13, Wednesday)

## KEYNOTE: BRING FLINK TO LARGE SCALE PRODUCTION

* crazy big scale flink/blink at Alibaba
* many of the changes Alibaba did to their fork blink are now flowing back to flink
* they introduced SQL on streams
  * because there are data engineers/analysts/business people that are not familiar with stream API programming, but know SQL very well
* having a data QL used to be an advantage of Spark over Flink

## UNIFIED STREAM AND BATCH PROCESSING WITH APACHE FLINK’S RELATIONAL APIS

* concept of the dynamic table
  * data manipulation event stream is materialized as a view on the current accumulated state
* you can perform queries on dynamic tables: continuous query
* types of queries are still limited but under heavy development, upcoming release flink 1.4

## FROM STREAMS TO TABLES AND BACK AGAIN: A DEMO OF FLINK’S TABLE & SQL API

* prototype cli SQL client
* Uber and Alibaba already have more sophisticated solutions (like zepelin?)
* any kind of UDFs can be created and used in SQL

## A LOOK AT FLINK’S INTERNAL DATA STRUCTURES AND ALGORITHMS FOR EFFICIENT CHECKPOINTING 

* 
* do not block the pipeline
* async checkpointing
  * async barrier snapshotting
* incremental snapshot
* state backend
  * HeapKeyedStateBackend
  * RocksDBKeyedStateBackend
* RocksDB
  * C++ over JNI
  * async
  * incremental
  * write optimized
* Heap Backend
  * copy-on-write hash map
  * multi versioning

# To check
* how exactly do watermarks work, watermarking strategies
* snapshot migration in flink 1.3, backward compatability
* rocksdb state backend
* storages: druid, pravega, NSDB


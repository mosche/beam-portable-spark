# Apache Beam PortableRunner using Python SDK on Apache Spark

Particularly when running on Mac or Windows, it can be challenging to get Beam's `PortableRunner` to work.
Communication with the SDK harness is expected to be done via `localhost`, requiring support for Docker's `host` network.
Unfortunately, the `host` network isn't available on Docker for Mac or Windows.

This repo contains a docker-compose file to showcase how to use the Beam's portability framework with 
Apache Spark and a Python worker pool on Docker (on a Mac / Windows) for testing purposes.

### Prerequisites

Until the Beam issue [#21092](https://github.com/apache/beam/issues/21092) is released, you have to build the image for the Spark job-server yourself.
```shell
git clone git@github.com:apache/beam.git
cd beam
./gradlew :runners:spark:3:job-server:container:docker
```

### Run job in docker

To start the entire stack including the sample wordcount job, run:
```shell
docker-compose up
```

### Run job from CLI
Alternatively you can also start the Spark job-server including necessary dependencies:
```shell
docker-compose up -d beam-job-server 
```
And then submit a job from your local shell:
```shell
python wordcount.py --runner=PortableRunner --job_endpoint=localhost:8099 --artifact_endpoint=localhost:8098 --environment_type=EXTERNAL --environment_config=pysdk:50000 --output=/tmp/result
```

In case you have to setup a Python environment for Beam first, you can use [`conda`](https://docs.conda.io/en/latest/miniconda.html) to do so:

```shell
# conda config --add channels conda-forge
conda create -n beamjob-py38 python=3.8 "apache-beam[gcp]=2.41.0"
conda activate beamjob-py38
```

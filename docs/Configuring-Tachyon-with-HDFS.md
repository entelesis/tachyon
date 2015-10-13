---
layout: global
title: Configuring Tachyon with HDFS
nickname: Tachyon with HDFS
group: Under Stores
priority: 3
---

This guide describes how to configure Tachyon with
[HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html)
as the under storage system.

# Initial Setup

To run a Tachyon cluster on a set of machines, you must deploy Tachyon binaries to each of these
machines. You can either
[compile the binaries from Tachyon source code](Building-Tachyon-Master-Branch.html), or
[download the precompiled binaries directly](Running-Tachyon-Locally.html).

Note that, by default, Tachyon binaries are built to work with Hadoop HDFS version `1.0.4`. To use
another Hadoop version, one needs to recompile Tachyon binaries from source code with the correct
Hadoop version set by either of following approaches. Assume `${TACHYON_HOME}` is the root directory
of Tachyon source code.

* Modify the `hadoop.version` tag defined in `${TACHYON_HOME}/pom.xml`. E.g., to work with Hadoop
`2.6.0`, modify this pom file to set `<hadoop.version>2.6.0</hadoop.version>` instead of
`<hadoop.version>1.0.4</hadoop.version>`. Then recompiling the source using maven.

```bash
$ mvn clean package
```

* Alternatively, you can also pass the correct Hadoop version in command line when compiling with
maven. For example, if you want Tachyon to work with Hadoop HDFS `2.6.0`:

```bash
$ mvn -Dhadoop.version=2.6.0 clean package
```

If everything succeeds, you should see
`tachyon-assemblies-{{site.TACHYON_RELEASED_VERSION}}-jar-with-dependencies.jar` created in
`assembly/target/` and this is the binary that you can run both Tachyon Master and Worker.

# Configuring Tachyon

To run Tachyon binary, we must setup configuration files. Create your configuration file from the
template:

```bash
$ cp conf/tachyon-env.sh.template conf/tachyon-env.sh
```

Then edit `tachyon-env.sh` file to set the HDFS Namenode address:

```bash
TACHYON_UNDERFS_ADDRESS=hdfs://HDFS_HOSTNAME:HDFS_PORT
```

# Running Tachyon Locally with HDFS

After everything is configured, you can start up Tachyon locally to see that everything works.

```bash
$ ./bin/tachyon format
$ ./bin/tachyon-start.sh local
```

This should start one Tachyon master and one Tachyon worker locally. You can see the master UI at
[http://localhost:19999](http://localhost:19999).

Next, you can run a simple example program:

```bash
$ ./bin/tachyon runTest Basic CACHE_THROUGH
```

After this succeeds, you can visit [HDFS webUI](http://localhost:50070) to verify the files and
directories created by Tachyon. For this test, you should see a file named:
`/tachyon/data/default_tests_files/BasicFile_CACHE_THROUGH`

To stop Tachyon, you can run:

```bash
$ ./bin/tachyon-stop.sh
```

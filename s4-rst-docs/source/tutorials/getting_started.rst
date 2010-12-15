Getting Started
===============

By default, S4 runs a cluster consisting of a single node. Also by default, S4 runs without a cluster management system (e.g., ZooKeeper). Overriding the defaults is fairly straightforward. 

The steps in this guide have been tested on:

* Red Hat Enterprise Linux 4
* Ubuntu 10.10

Download
--------

* ``mkdir s4image``
* ``cd s4image``
* `Download <http://s4.github.com/core/target/s4_core-0.2.1.0.tar.gz>`_ the S4 core tarball into the ``s4image`` directory.

  * Alternatively, you can :doc:`build S4 core </tutorials/build_core>` yourself.
* `Download <http://s4.github.com/examples/twittertopiccount/target/twittertopiccount-0.0.0.2.tar.gz>`_ the sample application into the ``s4image`` directory.

  * Alternatively, you can :doc:`build the sample application </tutorials/build_sample_app>` yourself.
* ``tar xzf s4_core-*.tar.gz``
* ``cd s4_apps``
* ``tar xzf ../twittertopiccount-*.tar.gz``
* Edit ``twittertopiccount/adapter_conf.xml`` and replace ``youruserid`` and ``yourpassword`` with an actual twitter userid and password.

Run
---

You should see a sample application under ``s4_apps`` called ``twittertopiccount``. This sample application listens to the twitter Spritzer and keeps track of the top 10 hash tags. You can try it as follows:

* Set the environment variable ``JAVA_HOME``. S4 scripts expect to find the java command at ``${JAVA_HOME}/bin``.
* ``cd ../bin``
* Start an S4 node:

  ``./s4_start.sh &``

This will print out some initial set up messages. The setup messages should end with something like this::

  [/home/robloc/githubimage/s4_apps/twittertopiccount/twittertopiccount_conf.xml]
  Adding processing element with bean name topicExtractorPE, id topicSeenPE
  adding pe: io.s4.example.twittertopiccount.TopicExtractorPE@6b496d
  Using ConMapPersister ..
  Adding processing element with bean name topicCountAndReportPE, id topicCountAndReportPE
  adding pe: io.s4.example.twittertopiccount.TopicCountAndReportPE@11f23e5
  Using ConMapPersister ..
  Adding processing element with bean name top10TopicPE, id top10TopicPE
  adding pe: io.s4.example.twittertopiccount.TopNTopicPE@2d189c
  Using ConMapPersister ..

Look in ``../s4_core/logs/s4_core`` for the S4 node's log4j log.

* Run the adapter:

The adapter will adapt Twitter status messages into events expected by the sample application. To run it, enter this command:

.. code-block:: bash

  ./run_adapter.sh -x -u ../s4_apps/twittertopiccount/lib/twittertopiccount-*.jar \
   -d ../s4_apps/twittertopiccount/adapter_conf.xml &

To check the current top 10 hash tags, look in this file: ``/tmp/top_n_hashtags``

Troubleshooting
---------------

* Process hanging waiting for lock

When running in red button mode (i.e., not using Zookeeper as a cluster manager), s4 processes use lock files in the ``s4_core/lock`` directory. If you've killed any s4 Java process with the ``KILL`` (9) signal, the lock file for that Java process may not get cleared out. Therefore, subsequent load generator or s4 node processes may hang waiting for the lock. You will see a message like the following::

    Process taken up by another process lockFile:/home/robbins/s4-0.1/s4_core/lock/s4_listenerLISTEN_PROCESS_1
    processAvailable:false

To avoid this issue, make sure you always use kill with the default signal. If you are running the process in the foreground, :kbd:`Control-c` also works fine.

If you run into trouble with lock files:
   
  * Kill all s4 processes (including the adapter)
  * Clear all files in ``s4_core/lock``
  * Try running the processes again


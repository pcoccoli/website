---
date: 2021-11-02
publishdate: 2021-11-02
published_url: "/posts/kestrel-sysflow-bheu21-open-hunting-stack"
title: "Setting Up The Open Hunting Stack in Hybrid Cloud With Kestrel and SysFlow"
author: "Xiaokui Shu"
publisher: "Open Cybersecurity Alliance"
featured_image: "/images/kestrel/huntflow.png"
summary: "How to set up the open hunting stack presented at Black Hat Europe 2021 Arsenal."
tag: "blog"
---

[Xiaokui Shu](https://xshu.net) &nbsp;·&nbsp; November 02, 2021 &nbsp;·&nbsp; 15 min read

In the [first blog](/posts/kestrel-2021-07-26) of our Kestrel blog series, we
showed how to install
[Kestrel](https://github.com/opencybersecurityalliance/kestrel-lang) and hunt
on a Windows machine monitored by
[Sysmon](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon). Let's
move forward to hunt in hybrid clouds where cloud workloads are monitored by
[SysFlow](https://github.com/sysflow-telemetry).  This blog will discuss the
setup of the open hunting stack presented at [Black Hat Europe
2021](https://www.blackhat.com/eu-21/). In our session [An Open Stack for
Threat Hunting in Hybrid Cloud With Connected
Observability](https://www.blackhat.com/eu-21/arsenal/schedule/index.html#an-open-stack-for-threat-hunting-in-hybrid-cloud-with-connected-observability-25112),
we hunt an APT in a hybrid cloud, which is a variant of a typical supply chain
attack yet implemented in a more stealthy manner. Join us to watch the hunt and
understand the open hunting stack.

<p>
  <a href="/images/kestrel/2021-11-02_stack_arch.png" target="_blank">
    <img src="/images/kestrel/2021-11-02_stack_arch.png" alt="Open Hunting Stack in Hybrid Cloud" width="750px"/>
  </a>
  <br/>
</p>

The open hunting stack in hybrid cloud shown above consists of four components:

1. [Kestrel](https://github.com/opencybersecurityalliance/kestrel-lang)
   provides the language to compose, share, and reuse huntflows.

2. [STIX-shifter](https://github.com/opencybersecurityalliance/stix-shifter)
   provides federated search to achieve cross-source queries.

3. [Elastic](https://www.elastic.co/) provides data storage and retrieval for
   log and alert management.

4. [SysFlow](https://github.com/sysflow-telemetry) provides connected
   observability in cloud workloads like containers.

### Spinning up a Mini-Stack To Play With

Want to get a sense of threat hunting in the cloud before deploying the open
hunting stack in a real cloud? Follow the two-step instruction to try the
mini-stack at home (`Python 3` and `docker` are required):

1. Clone the [sf-deployments
   repo](https://github.com/sysflow-telemetry/sf-deployments) from SysFlow and
   run `make build` and `make run` to spin up (i) a target container to monitor
   (named _attack_), (ii) SysFlow containers (named _sf-collector_ and
   _sf-processor_), and (iii) Elastic containers (named _elk_elasticsearch_ and
   _elk_kibana_).

2. Install Kestrel and the STIX-shifter Elastic-ECS module. Add a STIX-shifter
   data source pointing to the Elasticsearch service at the _elk_elasticsearch_
   container. Check detailed instructions in [Section: Setting Up Kestrel with
   STIX-shifter](#section:kestrelsetup) (note that the default indices created
   by [sf-deployments](https://github.com/sysflow-telemetry/sf-deployments) are
   `sysflow-*`).

Next let's formally setup the open hunting stack step-by-step in a cloud.

### Setting Up Elastic

We basically only need Elasticsearch for data storage and retrieval. SysFlow
will stream telemetry and alert data directly into Elasticsearch and Kestrel
will retrieve data via STIX-shifter directly from Elasticsearch. It is optional
to install the entire ELK: Elasticsearch, Logstash, and Kibana.

There are three options to setup Elasticsearch:

1. Try it for free as a [cloud
   service](https://www.elastic.co/cloud/elasticsearch-service/signup) on
   AWS/Azure/GCP.

2. Spin up a [Elasticsearch
   container](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html)
   in any public/private cloud.

3. Install and host our own [dedicated Elasticsearch
   server](https://www.elastic.co/guide/en/elasticsearch/reference/current/install-elasticsearch.html).

After we setup Elastic, the only thing to walk away with is the IP and port of
the Elasticsearch service. Let's say we get `elastic.ourcloud.com:9200`.

### Setting Up SysFlow

[SysFlow](https://github.com/sysflow-telemetry) provides connected
observability into cloud workloads. It efficiently monitors system calls using
eBPF ([Falco](https://falco.org/)) and lifts the huge number of low-level
system call events into flows such as FileFlow and NetworkFlow to better
organize telemetry data. SysFlow also performs real-time analytics, e.g.,
tagging ATT&CK TTPs according to [Falco rules](https://falco.org/docs/rules/).

Illustrated in the architecture figure above, SysFlow has two major components
*Collector* and *Processor*, which will be executed as two separate containers
in the cloud, besides all existing workload containers to monitor in the cloud.

Depending on the cloud environment, e.g., Docker/Helm/OpenShift, we can follow
the detailed [SysFlow deployment
instructions](https://sysflow.readthedocs.io/en/latest/deploy.html) to start
the two SysFlow containers in the cloud. To simplify the connection to
Elasticsearch, let's clone the [sf-deployment
repo](https://github.com/sysflow-telemetry/sf-deployments) and use the
deployment scripts in its
[/integrations/elk/sf/](https://github.com/sysflow-telemetry/sf-deployments/tree/master/integrations/elk/sf)
directory.

Before we start SysFlow, we need to modify the configuration to tell SysFlow:

1. Which workload container to monitor? Set `container.name=container1` in the
   [.env.no_elk](https://github.com/sysflow-telemetry/sf-deployments/blob/master/integrations/elk/sf/.env.no_elk)
   file to monitor the container named `container1` in our cloud. This
   condition instructs SysFlow collector to pass only records from this
   container (by default, SysFlow monitors the entire endpoint environment).

2. Where is our Elasticsearch service? Let's replace `172.17.0.1:9200` with
   `elastic.ourcloud.com:9200` with the credential of our Elasticsearch service
   in the
   [pipeline.tee.no_elk.json](https://github.com/sysflow-telemetry/sf-deployments/blob/master/integrations/elk/sf/resources/pipelines/pipeline.tee.no_elk.json)
   file (two sections).

3. Which index names in Elasticsearch to stream telemetry and alerts? Let's
   replace `sysflow-alerts` and `sysflow-events` with
   `container1-sysflow-alerts` and `container1-sysflow-events` in the
   [pipeline.tee.no_elk.json](https://github.com/sysflow-telemetry/sf-deployments/blob/master/integrations/elk/sf/resources/pipelines/pipeline.tee.no_elk.json)
   file.

Next let's start SysFlow containers and watch data flowing into Elastic:

```bash
# should be executed in directory /integrations/elk/sf/
make -f Makefile.no_elk run
```

### <a name="section:kestrelsetup"></a>Setting Up Kestrel with STIX-shifter

Assuming we will write and execute
[Kestrel](https://github.com/opencybersecurityalliance/kestrel-lang) hunt
playbooks in [Jupyter Notebook](https://jupyter.org/), let's install the
Kestrel runtime on our hunting machine (either a local laptop or a dedicated
hunting VM in our cloud). We will follow the [Kestrel Installation
Guideline](https://kestrel.readthedocs.io/en/latest/installation.html) to
install Kestrel in a Python virtual environment with the latest dependent
packages.

First, open a terminal in/into the hunting machine, create a clean Python
virtual environment, activate it, and update the virtual environment:

```bash
$ python -m venv huntingspace
$ . huntingspace/bin/activate
$ pip install -U pip setuptools wheel
```

Second, install Kestrel runtime with its Jupyter Notebook kernel:
```bash
# dependent packages such as `stix-shifter` will be automatically installed by `pip`
$ pip install kestrel-jupyter
$ python -m kestrel_jupyter_kernel.setup
```

Third, install the Elastic ECS connector of STIX-shifter:
```bash
$ pip install stix-shifter-modules-elastic-ecs
```

Fourth, define environment variables to configure a Kestrel data source `container1`:
```bash
$ export STIXSHIFTER_CONTAINER1_CONNECTOR=elastic_ecs
$ export STIXSHIFTER_CONTAINER1_CONNECTION='{"host":"elastic.ourcloud.com", "port":9200, "indices":"container1-sysflow-*"}'
$ export STIXSHIFTER_CONTAINER1_CONFIG='{"auth":{"id":"VuaCfGcBCdbkQm-e5aOx", "api_key":"ui2lp2axTNmsyakw9tvNnw"}}'
```

Note that we need to specify the indices as `container1-sysflow-*` so Kestrel
can correlate telemetry data with alerts (TTP tags) from the two SysFlow
indices.

We can add more data sources from the hybrid cloud. If the new data source goes
through Elastic, such as a Windows system monitored by
[Sysmon](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon) as we
demonstrated in our [Black Hat
session](https://www.blackhat.com/eu-21/arsenal/schedule/index.html#an-open-stack-for-threat-hunting-in-hybrid-cloud-with-connected-observability-25112),
just define a new set of environment variables to set it up:

```bash
$ export STIXSHIFTER_LAPTOP1_CONNECTOR=elastic_ecs
$ export STIXSHIFTER_LAPTOP1_CONNECTION='{"host":"elastic.ourcloud.com", "port":9200, "indices":"employee-laptop-1"}'
$ export STIXSHIFTER_LAPTOP1_CONFIG='{"auth":{"id":"VuaCfGcBCdbkQm-e5aOx", "api_key":"ui2lp2axTNmsyakw9tvNnw"}}'
```

If the additional data source is CarbonBlack/CrowdStrike/QRadar, we need to
install the corresponding [STIX-shifter
connector](https://github.com/opencybersecurityalliance/stix-shifter/blob/develop/OVERVIEW.md#available-connectors)
like what we did in the third step before setting up the data source in the
fourth step. Find examples in the [Kestrel
Documentation](https://kestrel.readthedocs.io/en/latest/tutorial.html#hunting-on-real-world-data).

### Hello World Hunt in the Hybrid Cloud

Our open hunting stack is ready! Let's start Jupyter Notebook and hunt. In the
terminal where we installed and setup Kestrel runtime:

```bash
$ jupyter notebook
```

Jupyter should report a URL to visit in a browser. We may need to configure
Jupyter to [enable access from specific/any
IP](https://stackoverflow.com/a/43500232) if this is a dedicated hunting server
in the cloud.

In a browser opening the Jupyter URL, we can create a Kestrel huntbook by
choosing the *Kestrel* kernel under the dropdown menu of *New Notebooks*.

![Kestrel Jupyter New Huntbook](/images/kestrel/2021-07-01_new_huntbook.jpg)

Let's write and execute (`Shift`+`Enter`) our hello world hunt step on `container1`:

```elixir
# get all `ping` processes on Oct 17, 2021 into Kestrel variable `hello`
hello = GET process FROM stixshifter://container1
        WHERE [process:name = 'ping']
        START t'2021-10-17T00:00:00Z' STOP t'2021-10-18T00:00:00Z'
```

![Hello Step Execution Summary](/images/kestrel/2021-11-02_hello_step_result.png)

The data pipeline is working. Then let's test the first hunt step in our [Black
Hat
session](https://www.blackhat.com/eu-21/arsenal/schedule/index.html#an-open-stack-for-threat-hunting-in-hybrid-cloud-with-connected-observability-25112)
using SysFlow TTP tags to simplify complex TTP pattern to write in Kestrel:

```elixir
exploits = GET process FROM stixshifter://container1
           # The TTP tag simplifies enum `process:name IN ('sh', 'bash', 'csh', ...)`
           # https://attack.mitre.org/techniques/T1059/
           WHERE [process:parent_ref.name = 'node' AND process:x_ttp_tags LIKE '%T1059%']
           START t'2021-10-17T00:00:00Z' STOP t'2021-10-18T00:00:00Z'
```

![Exploits Step Execution Summary](/images/kestrel/2021-11-02_exploits_step_result.png)

And testing the second hunt step in our [Black Hat
session](https://www.blackhat.com/eu-21/arsenal/schedule/index.html#an-open-stack-for-threat-hunting-in-hybrid-cloud-with-connected-observability-25112):

```elixir
# What did the exploited process do,
#   e.g., did it execute any commands?
#         did it probe the host info?
#         did it established a C&C?
#         did it do anything harmful?
#
# Let's find child processes of the exploited processes and display them chronically

exp_spawn = FIND process CREATED BY exploits
exp_act = GET process FROM exp_spawn WHERE [process:name != 'bash']
exp_act = SORT exp_act BY created ASC
DISP exp_act ATTR created, pid, parent_ref.pid, name, command_line
```

![EXP_ACT Step Execution Summary](/images/kestrel/2021-11-02_expact_step_result.png)

Then how to rank suspiciousness of processes using SIGMA rules? How to find
network traffic of a suspicious process? How to lookup geolocations of IP
addresses and pin IP on a map? How to do provenance tracking? How to follow
data-flow across network boundaries in a hybrid cloud? How to reveal the root
cause of an APT in a Windows machine? Join our [Black Hat
session](https://www.blackhat.com/eu-21/arsenal/schedule/index.html#an-open-stack-for-threat-hunting-in-hybrid-cloud-with-connected-observability-25112)
to find answers.

### Summary

In this blog, we go over the setup of an open hunting stack in hybrid cloud
[presented at Black Hat Europe
2021](https://www.blackhat.com/eu-21/arsenal/schedule/index.html#an-open-stack-for-threat-hunting-in-hybrid-cloud-with-connected-observability-25112).
Visit [SysFlow](https://github.com/sysflow-telemetry) to further understand its
advanced cloud-native monitoring and analyzing capabilities. Read our Kestrel
blog series to learn how to [start hunt from simple pattern
matching](http://localhost:1313/posts/kestrel-2021-07-26/), [perform
backward/forward tracking and apply analytic
steps](http://localhost:1313/posts/kestrel-2021-08-16/), and [write your own
Kestrel analytic steps](http://localhost:1313/posts/kestrel-custom-analytics/).

Until next time, happy threat hunting!

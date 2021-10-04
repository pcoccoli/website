---
date: 2021-09-01
publishdate: 2021-09-01
published_url: "/posts/kestrel-custom-analytics"
title: "Building Your Own Kestrel Analytics and Sharing With the Community"
author: "Paul Coccoli"
publisher: "Open Cybersecurity Alliance"
featured_image: "/images/kestrel/huntflow.png"
summary: "Turning your frequently used enrichment operation and/or machine learning procedure into a reusable and shareable hunting step in Kestrel."
tag: "blog"
---

Paul Coccoli &nbsp;·&nbsp; August 6, 2021 &nbsp;·&nbsp; 20 min read

In our [last blog
post](https://opencybersecurityalliance.org/posts/kestrel-2021-08-16/), we
hunted with a Kestrel analytics to enrich _network-traffic_ with domain
information. As a hunter, you may be using enrichment APIs, proprietary
detectors, Open Source software, or your own logic. How to use them in a
Kestrel hunt? How to make them reusable across different hunts? And how to
share the exciting analytics with the hunting community?

*In this blog post, I'll show you how to create your own
[Kestrel](https://github.com/opencybersecurityalliance/kestrel-lang) analytics
and how to share them with the hunting community. Analytics in Kestrel are a
form of "foreign function interface." You can think of them as plugins:
additional functionality that enable you to enrich your cybersecurity data
using external resources likes APIs, Open Source software, or your own logic.
More to read in the Kestrel documentation on [Applying
Analytics](https://kestrel.readthedocs.io/en/latest/tutorial.html#applying-an-analytics).*

While Kestrel supports multiple methods of running analytics via [its modular
architecture](https://kestrel.readthedocs.io/en/latest/language.html#data-and-analytics-interfaces),
this blog will focus on using [Docker](https://www.docker.com/). Using Docker
as the analytics interface lets you use any language you want to build an
analytics; in this blog post, I will give an example creating a Kestrel
analytics in Python. 

**Table of Content**

1. [The Kestrel Analytics Repository](#section:repo)
2. [Create Your Own Analytics Module](#section:create)
3. [Put Clustering to Work](#section:integrating)
4. [Dockerize it](#section:docker)
5. [Try It Out](#section:tryit)
6. [Wrap It up and Share](#section:share)

</br>
</br>

## <a name="section:repo"></a>The Kestrel Analytics Repository

To get started with Kestrel analytics, first clone the [Kestrel analytics repo](https://github.com/IBM/kestrel-analytics):

```shell
$ git clone https://github.com/opencybersecurityalliance/kestrel-analytics.git
```

Each analytics in the repo has a README.md that explains how to build and use it.  For example, the [suspiciousscoring](https://github.com/opencybersecurityalliance/kestrel-analytics/tree/release/analytics/suspiciousscoring) analytic's README shows how to build the docker image:

```shell
$ docker build -t kestrel-analytics-susp_scoring .
```

It also shows example [Kestrel statements](https://kestrel.readthedocs.io/en/latest/language.html#kestrel-command) that use the analytic:

```elixir
procs = GET process
        FROM file://samplestix.json
        WHERE [process:parent_ref.name = 'cmd.exe']
APPLY docker://susp_scoring ON procs
```

In this example, the first statement creates a [variable](https://kestrel.readthedocs.io/en/latest/language.html#kestrel-variable) called `proc` that gets assigned entities of type `process` (a [STIX Cyber Observable object type](http://docs.oasis-open.org/cti/stix/v2.0/cs01/part4-cyber-observable-objects/stix-v2.0-cs01-part4-cyber-observable-objects.html)) from a local [STIX 2.0 Bundle](http://docs.oasis-open.org/cti/stix/v2.0/cs01/part1-stix-core/stix-v2.0-cs01-part1-stix-core.html#_Toc496709292) that match the [STIX Pattern](https://docs.oasis-open.org/cti/stix/v2.0/stix-v2.0-part5-stix-patterning.html) `[process:parent_ref.name = 'cmd.exe']`.  This pattern matches processes whose parent name is `cmd.exe`.  Next, the `susp_scoring` analytics is applied to that `procs` variable; it will _score_ the `process` entities in `procs` and add a `x_suspicious_score` attribute (or "property" in STIX terminology) to each process.

## <a name="section:create"></a>Create Your Own Analytics Module

Creating your own analytics is easy, like _dockerizing_ any other piece of code.  To get started, we've included a [template](https://github.com/opencybersecurityalliance/kestrel-analytics/tree/release/template) that you can copy:

```shell
$ cp -r template/ analytics/my_analytic
```

Building the image is trivial:

```shell
$ cd analytics/my_analytic
$ docker build -t kestrel-analytics-my_analytic .
```

That's it!  It's now available to Kestrel.  Here's the example in action in a [Jupyter Notebook](https://jupyter-notebook.readthedocs.io/en/stable/) running the [Kestrel Jupyter Notebook Kernel](https://github.com/opencybersecurityalliance/kestrel-jupyter):

<p><img src="/images/kestrel/apply_example.png" alt="APPLY example" width="350px"/></p>

The example returns a [Kestrel display object](https://kestrel.readthedocs.io/en/latest/language.html#kestrel-command)---in this case, a small chunk of HTML that says, "Hello World! -- a Kestrel analytics".  It also adds a new attribute to all entities of the input variable `procs`, as can be seen using Kestrel's [DISP command](https://kestrel.readthedocs.io/en/latest/language.html#disp):

<p><img src="/images/kestrel/x_new_attr.png" alt="New attribute" width="420px"/></p>

## <a name="section:integrating"></a>Put Clustering to Work

Now let's make our analytics do something useful, something frequently used in exploratory data analysis---[clustering](https://en.wikipedia.org/wiki/Cluster_analysis). Since we decide to implement the analytics in Python, we can employee the [scikit-learn](https://scikit-learn.org/) library to build a [K-means](https://scikit-learn.org/stable/modules/clustering.html#k-means) clustering analytics.

Kestrel Analytics can add/modify attributes of entities from any variable
passed in as an argument. And it can return not only the updated variables but
also a display object for building visualization analytics (check
[APPLY](https://kestrel.readthedocs.io/en/latest/language.html#apply) command
for details).

For simplicity, let's define the analytics to build:

1. It only takes one argument or one Kestrel variable as input.
2. It adds one new attribute to the input objects: the cluster number each
   object got assigned to by the K-means algorithm.
3. It yields no display object---let's simply remove code for the display object.

</br>

The developers of analytics define the requirement and usage of analytics. Some
analytics are only applicable on a specific type of entities, e.g.,
`susp_scoring` on `process` entity variables, while some may be applicable to
multiple entity types.

K-means requires numerical inputs, and it makes more sense to execute it
against byte counts like `src_byte_count` and `dst_byte_count` of
_network traffic_ entities (`network-traffic` STIX cyber observable type) than
process name and command line of _process_ entities. To start simple, let's
only deals with one entity type---`network-traffic`---in our new Kestrel analytics.

Let's get some ideas of the data of which the analytics will be applied; I use
a `GET` statement to extract DNS connections from a synthetic dataset I have on
my laptop:

<p><img src="/images/kestrel/get_dns_conns.png" alt="GET DNS connections" width="706px"/></p>

Using the `INFO` command, we can see what attributes the entities in `dns_conns` have (and thus confirm that this STIX bundle did include the byte counts, which are optional):

<p><img src="/images/kestrel/info_dns_conns.png" alt="INFO dns_conns" width="1020px"/></p>

Note that `src_byte_count` and `dst_byte_count` appear in the list of "Entity Attributes."

Now that we have some numerical data, we're ready to try out K-means.  We'll want a way to tell K-means which attributes to look at, as well as how many clusters to find.  We can use Kestrel analytics parameters for that.  The [full syntax](https://kestrel.readthedocs.io/en/latest/language.html#apply) for the `APPLY` command is:

```
APPLY analytics_identifier ON var1, var2, ... WITH x=1, y=abc
```

The `WITH` clause allows us to send parameters to our analytic, so we'll use it to pick the attributes of the input data and specify the number of clusters.

Analytics parameters are passed to the docker container as environment variables, so when our container starts up, we'll need to read those values.

Let's look at the complete code, and then walk through it:

```python
import os

import pandas as pd
from sklearn.cluster import KMeans

# Kestrel analytics default paths (single input variable)
INPUT_DATA_PATH = "/data/input/0.parquet.gz"
OUTPUT_DATA_PATH = "/data/output/0.parquet.gz"

# Our analytic parameters from the WITH clause
# Kestrel will create env vars for them 
COLS = os.environ['columns']
N = os.environ['n']


def analytics(df):
    # Process our parameters
    cols = COLS.split(',')
    n = int(N)

    # Run the algorithm
    kmeans = KMeans(n_clusters=n).fit(df[cols])
    df['cluster'] = kmeans.labels_

    # return the updated Kestrel variable
    return df


if __name__ == "__main__":
    dfi = pd.read_parquet(INPUT_DATA_PATH)
    dfo = analytics(dfi)
    dfo.to_parquet(OUTPUT_DATA_PATH, compression="gzip")
```

The values of constants `INPUT_DATA_PATH` and `OUTPUT_DATA_PATH` are conventions in Kestrel for passing Kestrel variables in and out of the docker container.  The Kestrel runtime will write the first input variable's data (essentially a table) to `/data/input/0.parquet.gz`.  This is a gzipped [Parquet file](https://parquet.apache.org/).  If you have more than one input variable, replace the `0` in that path with `1`, `2`, etc (more details in [Kestrel docker analytics interface](https://kestrel.readthedocs.io/en/latest/source/kestrel_analytics_docker.interface.html)). As defined earlier, our clustering analytics will only handle one input variable. 

Since the analytics is expected to add attributes (or modify the existing ones), we write out the result in the same format, but different path: `/data/output/0.parquet.gz`.

Don't worry if you've never dealt with Parquet files before; the popular [Pandas](https://pandas.pydata.org/) package makes reading and writing Parquet a snap.  The `__main__` portion of our analytics uses Pandas to read in the input variable into a Pandas DataFrame, calls our `analytics` function, then writes out the result.

Reading the parameters is standard Python stuff:

```python
# Our analytic parameters from the WITH clause
# Kestrel will create env vars for them 
COLS = os.environ['columns']
N = os.environ['n']
```

Just grab the values of the environment variables, which have the same names are the parameters from our `WITH` clause.

In our `analytics` function, we have a little bit of work to do.  First, our parameters are just string values we read from the environment.  We want our list of attributes (which I called `columns` since we're now dealing with a Pandas DataFrame) to be an actual Python list, so we use the Python string method `split` to split it on commas into a list of column names.  Then we want `n`, our number of clusters, to be an actual integer.  Convert it using `int()`.

All that's really left to do is to pass our data into the `KMeans` algorithm itself.  We use the `fit()` method here---review the [K-means documentation](https://scikit-learn.org/stable/modules/clustering.html#k-means) if you would like to learn more about K-means (or unsupervised machine learning in general).  Notice that we pass `df[cols]`, not `df`.  We only want `KMeans` to see the columns we specified in our Kestrel `APPLY` statement.

Once `KMeans` finishes, the cluster number for each input object (in our case `src_byte_count` and `dst_byte_count` pairs) will be stored in its `labels_` member array. That's the new attribute we want, so we simply assign it as a new column `cluster` in our DataFrame.

We return our modified DataFrame, write back to the output path as a Parquet file, and we're done! Our container will exit and the Kestrel runtime will see our output file, and replace the values of our variable with the table in that file. That means our new column will now be visible as a new attribute in Kestrel.

## <a name="section:docker"></a>Dockerize It

We'll need to modify the example Dockerfile a little now, since we're adding some third party Python dependencies.  Aside from that, we'll fix up the comments at the top, but that's it:

```dockerfile
################################################################
#               Kestrel Analytics - scikit-learn clustering
#
# Build the analytics container:
#   docker build -t kestrel-analytics-my_analytic .
#
# Call the analytics in Kestrel
#   APPLY my_analytic ON my_var WITH n=N,columns=...
#
################################################################

FROM python:3

RUN pip install --upgrade pip && \
    pip install --no-cache-dir pandas pyarrow scikit-learn

WORKDIR /opt/analytics

ADD analytics.py .

CMD ["python", "analytics.py"]
```

Note that we added `scikit-learn` to the `pip` command that installs `pandas` and `pyarrow` (which is needed by pandas to read and write Parquet files). 

## <a name="section:tryit"></a>Try It Out

After rebuilding our docker image (using the same `docker build` command from above), we're ready to try out our analytics.  It's not a "Hello, World" anymore; now it's actually doing some analysis---k-means clustering.

In a Kestrel session in a jupyter notebook, we can run our new analytics with parameters:

```
APPLY docker://my_analytic ON dns_conns WITH n=2, columns=src_byte_count,dst_byte_count
```

Let's display our modified input variable:

<p><img src="/images/kestrel/new_column.png" alt="New column" width="1180px"/></p>

Notice the `cluster` column---it contains the cluster number picked by `KMeans`.  Since we asked for 2 clusters, that column will have either 0 or 1 in it.

Now we can filter our data using that column in Kestrel:

<p><img src="/images/kestrel/outliers.png" alt="Outliers" width="1180px"/></p>

Only 8 of the 1393 objects got assigned to cluster 1, so I called these `outliers`.

## <a name="section:share"></a>Wrap It up and Share

That's it; we're done.  We built a new Kestrel analytics, using `scikit-learn` and `pandas`.  Now we can cluster our security data in Kestrel like a data scientist!  All it took was dockerizing a small piece of code and following a few simple Kestrel conventions for passing data in and out of our container.

Time to polish it (maybe making it applicable to multiple entity types) and
make a pull request (PR) to the [Kestrel analytics
repo](https://github.com/IBM/kestrel-analytics) to share it with our
colleagues, friends, and other threat hunters in the community.

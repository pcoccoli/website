---
date: 2021-08-06
publishdate: 2021-08-06
published_url: "/posts/kestrel-custom-analytics"
title: "Building Custom Analytics with Kestrel"
author: "Paul Coccoli"
publisher: "Open Cybersecurity Alliance"
featured_image: "/images/kestrel/huntflow.png"
summary: "Tutorial on building custom external analytics that operate on cybersecurity data within a Kestrel threat hunting session."
tag: "blog"
---

Paul Coccoli &nbsp;·&nbsp; August 6, 2021 &nbsp;·&nbsp; 20 min read

In this blog post, I'll show you how to create your own analytics for use with the [Kestrel Threat Hunting Language](https://kestrel.readthedocs.io/en/latest/).  Analytics in Kestrel are a form of "foreign function interface.'' You can think of them as plugins: additional functionality that enable you to enrich your cybersecurity data using external resources likes APIs, Open Source software, or your own logic.  You can read more in the Kestrel documentation section on [Applying Analytics](https://kestrel.readthedocs.io/en/latest/tutorial.html#applying-an-analytics).

While Kestrel can support multiple methods of running analytics via [its modular architecture](https://kestrel.readthedocs.io/en/latest/language.html#data-and-analytics-interfaces), this blog will focus on using [Docker](https://www.docker.com/).  Using Docker as the analytics interface lets you use any language you want; in this blog post, I will give an example in Python. 

**Table of Content**

1. [The Kestrel Analytics GitHub Repo](#section:repo)
2. [Creating your own analytics module](#section:create)
3. [Integrating Third Party packages](#section:integrating)
4. [Dockerize it](#section:docker)
5. [Try it out](#section:tryit)
6. [Wrap it up](#section:wrap)

</br>
</br>

## <a name="section:setup"></a>The Kestrel Analytics GitHub Repo

To get started with Kestrel analytics, first clone the official analytics repo: [GitHub - opencybersecurityalliance/kestrel-analytics: Kestrel Analytics](https://github.com/IBM/kestrel-analytics)

```shell
$ git clone https://github.com/opencybersecurityalliance/kestrel-analytics.git
```

Each analytic in the repo has a README.md that explains how to build and use it.  For example, the [suspiciousscoring](https://github.com/opencybersecurityalliance/kestrel-analytics/tree/release/analytics/suspiciousscoring) analytic's README shows how to build the docker image:

```shell
$ docker build -t kestrel-analytics-susp_scoring .
```

It also shows example [Kestrel statements](https://kestrel.readthedocs.io/en/latest/language.html#kestrel-command) that use the analytic:

```shell
procs = GET process
        FROM file://samplestix.json
        WHERE [process:parent_ref.name = 'cmd.exe']
APPLY docker://susp_scoring ON procs
```

In this example, the first statement creates a [variable](https://kestrel.readthedocs.io/en/latest/language.html#kestrel-variable) called `proc` that gets assigned entities of type `process` (a [STIX Cyber Observable object type](http://docs.oasis-open.org/cti/stix/v2.0/cs01/part4-cyber-observable-objects/stix-v2.0-cs01-part4-cyber-observable-objects.html)) from a local [STIX 2.0 Bundle](http://docs.oasis-open.org/cti/stix/v2.0/cs01/part1-stix-core/stix-v2.0-cs01-part1-stix-core.html#_Toc496709292) that match the [STIX Pattern](https://docs.oasis-open.org/cti/stix/v2.0/stix-v2.0-part5-stix-patterning.html) `[process:parent_ref.name = 'cmd.exe']`.  This pattern is trying to match processes whose parent name is `cmd.exe`.  Next, the `susp_scoring` analytic is applied to that `procs` variable; it will "score" the `process` objects in `procs` and add a `x_suspicious_score` attribute (or "property" in STIX terminology) to each.

## <a name="section:create"></a>Creating your own analytics module

Creating your own analytic is easy, like "dockerizing" any other piece of code.  To get started, we've included a [template](https://github.com/opencybersecurityalliance/kestrel-analytics/tree/release/template) that you can copy:

```shell
$ cp -r template/ analytics/my_analytic
```

Building the image is trivial:

```shell
$ cd analytics/my_analytic
$ docker build -t kestrel-analytics-my_analytic .
```

That's it!  It's now available to Kestrel.  Here's the example in action in a [Jupyter Notebook](https://jupyter-notebook.readthedocs.io/en/stable/) running the [Kestrel Jupyter Notebook Kernel](https://github.com/opencybersecurityalliance/kestrel-jupyter):

![APPLY example](/images/kestrel/apply_example.png)

The example returns a "display" object - in this case, a small chunk of HTML that says, "Hello World! -- a Kestrel analytics".  It also adds a new attribute to its input variable `procs`, as can be seen using Kestrel's [DISP command](https://kestrel.readthedocs.io/en/latest/language.html#disp):
![New attribute](/images/kestrel/x_new_attr.png)

## <a name="section:integrating"></a>Integrating Third Party packages

Let's make our analytic do something useful now. [Clustering](https://en.wikipedia.org/wiki/Cluster_analysis) is a common technique in exploratory data analysis.  [scikit-learn](https://scikit-learn.org/stable/) contains some clustering algorithms; let's use [K-means](https://scikit-learn.org/stable/modules/clustering.html#k-means).

For simplicity, let's remove the display output from the analytic and just focus on adding a new attribute to the input objects: the cluster number each object got assigned to by the K-means algorithm.

K-means needs numerical inputs, whereas the process data in the example above will be non-numerical (strings like process name and command line).  Even process IDs (pids), while integers, are not really numerical: it makes no sense to ask for the difference between two pids.  While there are ways of dealing with such data in statistics and data science, I will take the easy way out and use a different dataset.  Network traffic data (the `network-traffic` STIX cyber observable type) often contains numerical values like `src_byte_count` and `dst_byte_count`; this will be easier to work with for now.

In the screenshot below, I use a `GET` statement to extract DNS connections from a synthetic dataset I have on my laptop:

![GET DNS connections](/images/kestrel/get_dns_conns.png)

Using the `INFO` command, we can see what attributes the objects in `dns_conns` have (and thus confirm that this STIX bundle did include the byte counts, which are optional):

![INFO dns_conns](/images/kestrel/info_dns_conns.png)

Note that `src_byte_count` and `dst_byte_count` appear in the list of "Entity Attributes."

Now that we have some numerical data, we're ready to try out K-means.  We'll want a way to tell K-means which attributes to look at, as well as how many clusters to find.  We can use Kestrel analytics parameters for that.  The full syntax for the `APPLY` command is:

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

The values of constants `INPUT_DATA_PATH` and `OUTPUT_DATA_PATH` are conventions in Kestrel for passing Kestrel variables in and out of the docker container.  The Kestrel runtime will write the first input variable's data (essentially a table) to `/data/input/0.parquet.gz`.  This is a gzipped [Parquet file](https://parquet.apache.org/).  If you have more than 1 input variable, replace the `0` in that path with `1`, `2`, etc.  Our clustering analytic will only work with 1 input variable. 

Since the analytic is expected to add attributes (or modify the existing ones), we write out the result in the same format, but different path: `/data/output/0.parquet.gz`.

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

All that's really left to do is to pass our data into the `KMeans` algorithm itself.  We use the `fit()` method here - review the `KMeans` documentation if you would like to learn more about K-means (or unsupervised machine learning in general).  Notice that we pass `df[cols]`, not `df`.  We only want `KMeans` to see the columns we specified in our Kestrel `APPLY` statement.

Once `KMeans` finishes, the cluster number for each input object (in our case `src_byte_count` and `dst_byte_count` pairs) will be stored in its `labels_`member array. That's the new attribute we want, so we simply assign it as a new column "cluster" in our DataFrame.

We return our modified DataFrame, write back to the output path as a Parquet file, and we're done! Our container will exit and the Kestrel runtime will see our output file, and replace the values of our variable with the table in that file. That means our new column will now be visible as a new attribute in Kestrel.

## <a name="section:docker"></a>Dockerize it

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

## <a name="section:tryit"></a>Try it out

After rebuilding our docker image (using the same `docker build` command from above), we're ready to try out our analytic.  It's not a "Hello, World" anymore; now it's actually doing some analysis.

In a Kestrel session in a jupyter notebook, we can run our new analytic with parameters:

```
APPLY docker://my_analytic ON dns_conns WITH n=2, columns=src_byte_count,dst_byte_count
```

Let's display our modified input variable:

![New column](/images/kestrel/new_column.png)

Notice the "cluster" column - it contains the cluster number picked by `KMeans`.  Since we asked for 2 clusters, that column will have either 0 or 1 in it.

Now we can filter our data using that column in Kestrel:

![Outliers](/images/kestrel/outliers.png)

Only 8 of the 1393 objects got assigned to cluster 1, so I called these `outliers`.

## <a name="section:wrap"></a>Wrap it up

That's it; we're done.  We built a new Kestrel analytic, using scikit-learn and pandas.  Now we can cluster our security data in Kestrel like a data scientist!  All it took was dockerizing a small piece of code and following a few simple Kestrel conventions for passing data in and out of our container.

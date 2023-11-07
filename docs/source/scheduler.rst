Dask client and Scheduler
===========================


It is not essential to use a client/scheduler-type structure, but for most workloads it makes sense.

In Dask the *client* object is associated to a *cluster* which consists of a scheduler and 1 or more workers which actually run the calculations. Schedulers can be *local*, where the parallelism is obtained using threads, or *distributed* which allows other resources to be used. 

Important cluster types include:

1. Simple distributed
2. Jobqueue (e.g. SlurmCluster)
3. MPI 
4. LocalCUDACluster (for GPUs)


We will now look at some example of these.

Distributed Cluster
---------------------

For example:


.. code-block:: python

  from dask.distributed import Client
  client = Client()  # Connect to distributed cluster and override default
  df.x.sum().compute()  # This now runs on the distributed system

After the client object is set up, all the following python code is run in the cluster. 
   

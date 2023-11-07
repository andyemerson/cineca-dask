Dask client and Scheduler
===========================


It is not essential to use a client/scheduler-type structure, but for most workloads it makes sense.

In Dask the *client* object is associated to a *cluster* which consists of a scheduler and 1 or more workers which actually run the calculations. Schedulers can be *local*, where the parallelism is obtained using threads, or *distributed* which allows other resources to be used. 
In the vast majority of cases we prefer the distributed-type since threading can be very inefficient for Python programs.

Important distributed scheduler types include:

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

After the client object is set up, all the following python code is run in the cluster. This type of cluster/scheduler would be suitable for a *cloud* environment.

Jobqueue
---------------

For HPC systems such as Leonardo where we need to interact with the batch system (e.g. SLURM of Leonardo), this type is convenient because the workers are defined as separate SLURM jobs.

.. code-block:: python
   ::linenos::

   from distributed import Client
   from dask_jobqueue import SLURMCluster
   # Next step is to start a SLURM Cluster
   # we define the characteristics of eack worker
   cluster = SLURMCluster(cores=1,
                       processes=1,
                       memory="16GB",
                       account="cin_staff",
                       walltime="00:30:00",
                       interface="ib0",
                       job_extra_directives=['--tasks-per-node=1']
                       )
   # Add more workers (i.e. SLURM jobs)
   cluster.scale(2)

When we define the cluster we need to define the characteristics of the worker jobs. This we do with the usual SLURM job definitions (remember each worker will be a SLURM jobs).
In the above note the use of `job_extra_directives=[]` to provide job information that `dask_jobqueue` does not define. Note also the use of `cluster.scale()` to set the number of workers.

.. warning::
   The DASK idea of what the appropriate SLURM job parameters should be, can (and does) vary as to what we expect on, say, Leonardo.
   Important to explicitly set the SLURM parameters or use defaults (see below).


Setting SLURM  job defaults
~~~~~~~~~~~~~~~~~~~~~~~~~~~~


   

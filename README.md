# Using Dedicated Node on DISCO Cluster

## Using Rstudio on Dedicated Node

1. Make sure you are on Broad network (either on Broad premises or connected via Broad VPN)
1. Login to DISCO login node

   ```
   ssh <username>@login00.broadinstitute.org
   ```

1. Login to Macosko lab dedicated node from the login node

   <!--ssh <username>@slurm-bits-bigmem-d002-->
   ```
   srun -C container --nodes=1  --mem=128GB --cpus-per-task=4 --partition=hpcx_macosko --pty /bin/bash
   ```

1. Make a directories ~/rstudio/packages and ~/rstudio/workdir

   ```
   mkdir -p ~/rstudio/packages -p ~/rstudio/workdir
   ```

1. Determine _internal_ip_ of node

   ```
   hostname -i
   ```

1. Pick a unique _port_number_ (e.g. any number in 8xxx range will do). 

1. Start Rstudio server using the following command. Make sure to replace `pick_a_password` and `port_number` with your own choices before using the command.
   ```
   podman run --name rstudio_$(whoami) -tid -e PASSWORD=<pick_a_password> -p <port_number>:8787 -v "$(realpath ./rstudio):/rstudio:rw" rocker/rstudio
   ```
   When running this command first time, a small menu will appear in the commandline presenting options available to fetch the container image from. Select the option starting with ```docker.io/library/```.
   <!--```-->
   <!--podman run --rm -ti -e PASSWORD=<pick_a_password> -p <port_number>:8787 -v "$(realpath ./rstudio):/rstudio:rw" rocker/rstudio-->
   <!--```-->
1. Open browser and connect to Rstudio server by typing the following in the address bar:
   ```
   <internal_ip>:<port_number>
   ```
1. In the login dialog box username should be `root` and password should be the `pick_a_password` specified couple of steps earlier.

1. Set the workdir and library path in Rstudio console _(needs to be run each time Rstudio server is started)_

   ```
   setwd('rstudio/workdir')
   .libPaths(c("/rstudio/packages", .libPaths()))
   ```

1. Thats it! You can work in Rstudio as usual. To stop Rstudio server: save work, logout from Rstudio in browser. To work with Rstudio again - we may can directly open the following Rstudio link in the browser.
   ```
   <internal_ip>:<port_number>
   ```
   Also, make sure to place all your code and data within the following working directory to be visible in Rstudio:
   ```
   ~/rstudio/workdir
   ```

## Appendix

- Unlike cloud VMs, resources on this DISCO node would be shared among several
  users simultanously. Although there is no need to _turn off_ the machine, the
  memory on this machine is shared among all active users. Therefore, it would
  help if everyone clears their R environment when they no longer need the
  objects to be loaded and also remember to log off Rstudio session when done.
  This would make the memory available for use by any other users.

- If you are not planning to use Rstudio for an extended period of time, it'd be good practice to stop the container using the following command:

  ```
  podman stop rstudio_<userid>
  ```

  and restart the container when needed using the following command.

  ```
  podman start rstudio_<userid>
  ```

  This would be akin to stoping the cloud VM instance.

- It would be good to set maximum resource usage limit for memory so that your container does not end up using more memory than expected and also for compute resource to be available for others in lab. This can be set by

  ```
  install.packages("unix")
  library(unix)
  rlimit_as(1e100)  # sets availbable memory to ~100GB
  ```

<!---->
<!--     ``` -->
<!--     ulimit -Sv max_mem_limit_in_kilobytes -->
<!--     ``` -->

- To run a batch job on the dedinated node, submit to partition `hpcx_macosko`

- For tranfering data between local machine and DISCO, use `rsync`.

- For transering data between DISCO and Google Bucket, see [here](https://broad.service-now.com/kb_view.do?sys_kb_id=e66ee8124777869014397fdc416d437b&sysparm_rank=1&sysparm_tsqueryId=87f8b0bd4784d25014397fdc416d43de).

- If your chosen _port_number_ is already taken, then try a different port or use the following command to identify a _port_number_ is not already being used by someone else.

  ```
  lsof -i -P
  ```

## References

\* BITS manuals require Broad credentials for access

- [BIS manual for using containers](https://backstage.broadinstitute.org/docs/default/component/disco-docs/using-containers/)
- [BIS manual for using DISCO](https://broad.service-now.com/kb_view.do?sys_kb_id=a6c74cb147d6a51411484438946d430e&sysparm_rank=1&sysparm_tsqueryId=5f7df45593bf829041a6b8327cba10c8)
- [BITS manual for using containers on DISCO](https://broad.service-now.com/kb_view.do?sys_kb_id=8923f956479aa91411484438946d4383)
- [Rocker project](https://rocker-project.org/) for Rstudio in a container
- [BITS manual for transfering data between DISCO and Google Bucket](https://broad.service-now.com/kb_view.do?sys_kb_id=e66ee8124777869014397fdc416d437b&sysparm_rank=1&sysparm_tsqueryId=87f8b0bd4784d25014397fdc416d43de)

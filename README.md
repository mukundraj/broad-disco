# Using Dedicated Node on DISCO Cluster


## Using Rstudio on Dedicated Node

1. Make sure you are on Broad network (either on Broad premises or connected via Broad VPN)
1. Login to DISCO login node
    ```
    ssh <username>@login00.broadinstitute.org
    ```

1. Login to Macosko lab dedicated node from the login node
    ```
    ssh <username>@slurm-bits-bigmem-d002

    ```
1. Make a directories ~/rstudio/packages and ~/rstudio/workdir
    ```
    mkdir -p ~/rstudio/packages -p ~/rstudio/workdir
    ```

1. Determine *internal_ip* of node
    ```
    hostname -i
    ```

1. Pick a unique *port_number* (can be any number between 8000-9000). Then use following command to ensure that *port_number* is not already being used by someone else.

    ```
    lsof -i -P
    ```

1. Start Rstudio server
    ```
    podman run --rm -ti -e PASSWORD=<pick_a_password> -p <port_number>:8787 -v "$(realpath ./rstudio):/rstudio:rw" rocker/rstudio
    ```
1. Open browser and connect to Rstudio server by typing the following in the address bar:
    ```
    <internal_ip>:<port_number>
    ```
1. In the login dialog box username should be ```root``` and password should be the ```pick_a_password``` specified couple of steps earlier.

1. Set the workdir and library path in Rstudio console *(needs to be run each time Rstudio server is started)*
    ```
    setwd('rstudio/workdir')
    .libPaths(c("/rstudio/packages", .libPaths()))
    ```

1. Thats it! You can work in Rstudio as usual. To stop Rstudio server, save work, logout from Rstudio in browser, and use Control-C in console to stop Rstudio server.


## References

\* BITS manuals require Broad credentials for access

- [BIS manual for using containers](https://backstage.broadinstitute.org/docs/default/component/disco-docs/using-containers/)
- [BIS manual for using DISCO](https://broad.service-now.com/kb_view.do?sys_kb_id=a6c74cb147d6a51411484438946d430e&sysparm_rank=1&sysparm_tsqueryId=5f7df45593bf829041a6b8327cba10c8)
- [BITS manual for using containers on DISCO](https://broad.service-now.com/kb_view.do?sys_kb_id=8923f956479aa91411484438946d4383)
- [Rocker project](https://rocker-project.org/) for Rstudio in a container






# cron-github-runner
This simple utility makes persisting [GitHub Actions self-hosted runners](https://docs.github.com/en/actions/hosting-your-own-runners) with cron jobs easy 👍. The script itself should be run from the [NCAR HPC cron service](https://ncar-hpc-docs.readthedocs.io/en/latest/compute-systems/additional-resources/cron), but your runner itself will run on a random login node on the selection cluster.

> [!CAUTION]
> Use of self-hosted runners with **public** repositories is [strongly discouraged](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/about-self-hosted-runners#self-hosted-runner-security). Please contact [NCAR Research Computing Help](http://rchelp.ucar.edu/) first if you want to do this!

## Quickstart

Get started by following these steps:
* Install your GitHub self-hosted runner on **GLADE**
* Configure the runner with `config.sh` (and test it)
* Sign into `cron.hpc.ucar.edu`
* Add a cron job that calls `cron-github-runner`
  * Specify whether your runner will operate on `derecho` or `casper`

**Example cron job**
```sh
# Check that the runner is active every 5 minutes
*/5 * * * * $HOME/cron-github-runner/cron-github-runner --machine derecho $WORK/runners/hpc-runner
```

## Advanced Usage

Once set up, you can always leave the cron job active (unless you are totally finished with the runner). You can start and stop the runner using the script from the command-line on the cron server. The script will also query information about the runner, and point you to logs that can be useful for debugging.

```
Usage: ./cron-github-runner --machine MACHINE RUNNERDIR
       ./cron-github-runner --info            RUNNERDIR
       ./cron-github-runner --resume          RUNNERDIR
       ./cron-github-runner --stop            RUNNERDIR

This script has three usage modes:

It can be used within a cron job to either start or keep alive a self-hosted HPC
runner on Derecho or Casper. Before you run this script, you will need to install
the GitHub Actions self-hosted runner application for Linux and the run the
"config.sh" script to set up the runner.

Once the runner is configured, add a cron job on cron.hpc.ucar.edu that calls
this script, providing the desired machine and the path to the configured runner.
This script will start the runner if it is not already started. If it is already
running, it will do nothing.

Example cron:

# Check that the runner is active every 5 minutes
*/5 * * * * /glade/work/vanderwb/repos/cron-github-runner/cron-github-runner --machine derecho /glade/work/vanderwb/runners/hpc-runner

Alternatively, the script can be called on the command line to either stop or
resume runner activity. If --stop is used, the runner will be killed if active
and the cron job will not try to restart it. If --resume is used, this hold on
the cron job will be released and it will try to restart the runner.

Options:
    -h, --help              print this help message
    -i, --info              print information about the runner at RUNNERDIR
    -m, --machine MACHINE   cron mode; specify derecho or casper for the machine
    -r, --resume            release hold on cron job and restart runner
    -s, --stop              kill runner if active and impose hold on cron job
```

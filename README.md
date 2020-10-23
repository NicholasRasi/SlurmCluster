# Slurm Cluster
Ansible playbooks to setup a multinode cluster with Slurm.

3 playbooks for the cluster initialization:

- controllers (slurmctdl)
- workers (slurmd)
- NAS (for network storage)

\+ 1 extra playbook for benchmarks initialization ([http://mvapich.cse.ohio-state.edu/benchmarks/](http://mvapich.cse.ohio-state.edu/benchmarks/), [mpiBench](https://github.com/LLNL/mpiBench))

\+ 1 extra playbook for HPC containers initialization ([Singularity](https://github.com/hpcng/singularity/), [CharlieCloud](https://github.com/hpc/charliecloud), [Sarus](https://github.com/eth-cscs/sarus]), + [DockerCE](https://github.com/docker/docker-ce) (for convenience))

---

The playbooks (controllers, workers, nas) will setup:

- hostname
- ssh connection
- ntp synch
- nfs server and client
- Munge and key
- Slurm (controller and worker)
- helpers

> It can be used on Azure with the CentOS-based HPC images ([Rogue Wave Software / OpenLogic](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/openlogic.centos-hpc)) to quickly deploy a cluster with MPI and pre-installed drivers for Infiniband (e.g. on HB60).

## Setup Local
- Install Ansible and deps:
```
virtualenv env
source env/bin/activate
pip install -r requirements.txt
```
- Edit ```inventory``` as needed
- Edit ```conf/hosts``` as needed the init controllers, nas and workers

### Setup Single Components

#### Setup Controllers
- Edit ```conf/slurm.conf``` as needed ([info](https://slurm.schedmd.com/configurator.html))
- Edit ```conf/cgroup.conf``` as needed ([info](https://slurm.schedmd.com/cgroups.html))

```
ansible-playbook -i inventory init_controllers.yml
```

#### Setup NAS
A folder called ```/home/user/workspace``` is created and exported. It is automounted on workers

If the NAS and the controller are on the same node, then run
```
ansible-playbook -i inventory init_nas.yml
```

otherwise run
```
ansible-playbook -i inventory init_nas.yml --extra-vars "external_nas=Y"
```

#### Setup Workers
```
ansible-playbook -i inventory init_workers.yml
```

#### Setup Container
The playbook will install Docker, Singularity, Charliecloud and Sarus

- Edit ```init_containers.yml``` vars to set the containers release versions
```
ansible-playbook -i inventory init_containers.yml
```

#### Setup Benchmarks
- Edit ```init_containers.yml``` vars to set the containers release versions
```
ansible-playbook -i inventory init_benchmarks.yml
```

### Setup All
Execute all the playbooks with:
```
ansible-playbook -i inventory init_all.yml
```

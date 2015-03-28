# Overview

Microservices infrastructure is a modern platform for rapidly deploying globally distributed services

## Features

* [Mesos](http://mesos.apache.org) cluster manager for efficient resource isolation and sharing across distributed services
* [Marathon](https://mesosphere.github.io/marathon) for cluster management of long running containerized services
* [Consul](http://consul.io) for service discovery 
* [Docker](http://docker.io) container runtime
* Multi-datacenter support
* High availablity

#### Architecture

The base platform contains control nodes that manage the cluster and any number of compute nodes. Containers automatically register themselves into DNS so that other services can locate them.

![Single-DC](docs/_static/single_dc.png)

Once WAN joining is configured, each cluster can locate services in other data centers via DNS or the [Consul API](http://www.consul.io/docs/agent/http.html). 

![Mult-DC](docs/_static/multi_dc.png)

#### Control Nodes

The control nodes manage a single datacenter.  Each control node runs Consul for service discovery, Mesos leaders for resource scheduling and Mesos frameworks like Marathon. 

In general, it's best to provision 3 or 5 control nodes to achieve higher availability of services. The Consul Ansible role will automatically bootstrap and join multiple Consul nodes. The Mesos Ansible role will provision highly-availabile Mesos and ZooKeeper environments when more than one node is provisioned. 

![Control Node](docs/_static/control_node.png)

#### Compute Nodes

The compute nodes launch containers and other Mesos-based workloads. [Registrator](https://github.com/gliderlabs/registrator) is used to update Consul as containers are launched and exit. 

![Compute Node](docs/_static/compute_node.png)

## Getting Started

A Vagrantfile is provided that provisions everything on a single VM. To run (ensure that your sytem has 4GB or RAM free):

```shell

vagrant up
```

### Deploying on multiple servers
If you already have running instances (Centos7 is the only Linux distribution supported at this time), do the following to deploy the software:

1. Install the software components: `sudo pip install -r requirements.txt`
2. Create an [Ansible inventory](http://docs.ansible.com/intro_inventory.html) file. You can use the the following files as examples, replacing the host names with your instances: 
	- [`inventory/1-datacenter`](inventory/1-datacenter)
	- [`inventory/2-datacenter`](inventory/2-datacenter) Multi-DC with WAN join. Ensure that DCs have network connectivity to each other, especially for ports 8300-8302. 
3. Set up security. Run: `./auth-setup` 
4. Run `ansible-playbook -i <your_inventory_file> site.yml -e @security.json`


The [Getting Started Guide](https://microservices-infrastructure.readthedocs.org/en/latest/getting_started/index.html) covers multi-server and OpenStack deployments.

## Documentation
All documentation is located at [https://microservices-infrastructure.readthedocs.org](https://microservices-infrastructure.readthedocs.org/en/latest). 

To build the documentation locally, run:

```shell
pip install -r requirements.txt
cd docs
make html
```

```

##Roadmap

- [x] Mesos
- [x] Marathon
- [x] Consul
- [x] Multi-datacenter
- [x] High availablity
- [x] Manage Linux user accounts
- [x] Authentication and authorization for Consul
- [ ] Authentication and authorization for Mesos
- [x] Authentication and authorization for Marathonk
- [ ] Application load balancer based on HAProxy and consul-template
- [ ] In-service Upgrade and Rollback
- [ ] Self maintaining system (log rotation, automatic instance replacement)
- [ ] Autoscaling of compute nodes (with HashiCorp Atlas)
- [ ] Immutable deployment (with HashiCorp Atlas + Packer)
- [ ] Kafka Mesos framework for container logs and application event bus
- [ ] ElasticSearch Mesos framework for container logs
- [ ] Kubernetes Mesos framework
- [x] Support for Vagrant
- [x] Support for OpenStack
- [ ] Support for Apache CloudStack
- [ ] Support for Amazon Web Services
- [ ] Support for Microsoft Azure
- [ ] Support for Google compute Engine
- [ ] Support for VMware vSphere

Please see [milestones](https://github.com/CiscoCloud/microservices-infrastructure/milestones) for more details on the roadmap.

##License
-------
Copyright © 2015 Cisco Systems, Inc.

Licensed under the [Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0) (the "License").

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.

This product includes software developed by the OpenSSL Project for use in the
OpenSSL Toolkit (http://www.openssl.org/)

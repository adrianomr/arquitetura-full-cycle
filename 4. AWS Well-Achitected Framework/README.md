# AWS Well-Architected Framework

## Cloud Solutions Architecture

Independetly of your cloud provider, there are good practices that every Cloud Solution should follow. Each provider usually has a document with these practices. For AWS, the document is called AWS Well Architected Framework. This framework brings good practices for the following principles:

* Operational Excellence
* Security
* Reliability
* Eficiency and performance
* Costs optimization
* Susteinability

### Operational Excellence

The capacity to offer suport to development and execute workloads with efficinecy, get data about your operations and continuosly improve suport processes and procedures to add value to your business. Develop team must be able to work. Everithing should work well, and when there is a problem you should be able to find it easyly.

#### Principles

* Infraestructure as code (IaC)
* Faça mudanças frequentes, pequenas e reversíveis
* Improve operatinal procedures frequently
* Antecipate failures
* Learn from all operatinal failures

### Security

Security is about taking advantage of cloud solutions to protect data, systems and assets.

#### Principles

* Strong identity solutions (infra and systems) (auth)
* Traces
* Use all security layers
* Protect in transit and persisted data
  * Comunication certificates and not allowing external connection to data
* Keep people away from data
* Get ready for security events

### Reliability

Capacity to execute a functionality correctly when needed. To operate and test workload through system lifecycle

#### Principles

* Automatic recover from failures (self healing)
* Recover procedured tests
* Horizontal scale
* Don't guess system capacity
  
### Eficiency and Performance

Capacity to utilize computational resources eficiently to attend system requisits. Keep this efficiency when demand changes and tecnologies evolve.

#### Principles

* Democratize advanced tecnologies
* Became global in minutes
* Utilize serverless architectures
* Experiment frequently
* Consider mechanical simpaty

### Costs Optimization

Capacity to execute a system paying the lower price possible.

#### Principles

* Cloud Finance Management
* Choose a consumption model
* Have general metrics
* Stop spending money in things that dont give a competitive advantage
* Analyze and attribute expenses

### Sustenability

Reduce the amount of resources needed with efficiency

#### Principles

* Understand resources impact
* Have sistenability goals
* Maximize utilization
* Antecipate and adopt new hardware and software offers
* Utilize managed services

### General Principles

* Stop guessing your capacities necessity
* Production scaling tests
* Automatize architecture experimentation
* Allow evolutive architectures
* Guide your architecture using data
* Improve during production time

### 3 Nivels of Losutions Architecture

* Business oriented Architecture (nivel 0)
* Techinical oriented Arquitecture (nível 1)
* Deployment oriented Architecture (nível 2)
# Architecture Decision Records

- [Crowd Sourcing: Eliminate direct RDBMS access: IN_PROGRESS](#crowd-sourcing-eliminate-direct-rdbms-access)
- [Cache above RDMS: PROPOSED](#choose-cache-above-rdbms)
- [Messaging Queue System: PROPOSED](#choose-messaging-queue-system)
- [Continous Intelligent Data Pipelines: PROPOSED](#continuous-intelligent-data-pipeline)

## Discuss

[![Gitter](https://badges.gitter.im/Vakyansh/community.svg)](https://gitter.im/Vakyansh/community?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)


## **Crowd Sourcing: Eliminate direct RDBMS access**

- Status: IN_PROGRESS
- Driver(s): [Rajat Singhal](https://github.com/srajat84)
- Approver(s): Pramod Verma, Vivek Raghavan
- Impact Area: High Level Architecture
- Date: 2021-03-24

Technical Story: [Eliminate direct RDBMS access](https://project-sunbird.atlassian.net/browse/SOC-2)

### Context and Problem Statement

Currently, the application service directly talks with RDBMS to fetch the sentences shown while doing Contributions and to fetch contributions while doing the Validations.

When the application will scale and more features are added, there may be some contention at RDBMS layer.

See [current architecture](https://open-speech-ekstep.github.io/crowdsource_platform/#architecture)

### Solutions

- Add cache layer over RDMS for faster reads
- Write to topics asynchronously for faster writes

### Tech Choices

- For cache : AWS ElasticCache - Redis, Apache Ignite : [ADR](#choose-cache-above-rdbms)
- For queue: AWS Kafka, AWS Kinesis : [ADR](#choose-messaging-queue-system)

### Proposed Architecture

![ADR](img/crowdsource/adr1.png)

#### Positive Consequences

- RDBMS will be off loaded from frequent reads and writes
- Users will experience lower latency
- System will be able to scale easily at data layer
- Audio processing like 'automatic validation', 'SNR' etc can be done in 'Contributions processor'
- Contribution processor can be in any other langauge like Python which is more suited for audio processing

#### Negative Consequences

- Overhead of maintaining cache and queue system
- The user experience will have to change due to async behaviour of the system

### Decision Outcome

WIP

## **Choose Cache above RDBMS**

- Status: PROPOSED
- Deciders: [Rajat Singhal](https://github.com/srajat84), Heera Ballabh, Soujyo Sen
- Impact Area: Tools and frameworks
- Date: 2021-03-24

Technical Story: []

### Context and Problem Statement

### Options

AWS ElasticCache(Redis), Apache Ignite

### Solutions

### Decision Outcome

```not yet decided```


## **Choose messaging queue system**

- Status: PROPOSED
- Deciders: [Rajat Singhal](https://github.com/srajat84), Heera Ballabh, Soujyo Sen
- Impact Area: Tools and frameworks

- Date: 2021-03-24

Technical Story: []

### Context and Problem Statement

### Options

Apache Kafka, AWS Kinesis

### Solutions

### Decision Outcome

```not yet decided```

## **Continuous Intelligent Data Pipeline**

- Status: PROPOSED
- Deciders: [Rajat Singhal](https://github.com/srajat84), Pramod Verma, Vivek Raghavan
- Impact Area: High Level Architecture
- Date: 2021-03-24

Technical Story: [Continous Intelligent Data Pipelines](https://project-sunbird.atlassian.net/browse/SOC-3)

### Context and Problem Statement

### Solutions

### Decision Outcome
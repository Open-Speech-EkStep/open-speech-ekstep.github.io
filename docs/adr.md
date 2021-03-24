# Architecture Decision Records

## Discuss

[![Gitter](https://badges.gitter.im/Vakyansh/community.svg)](https://gitter.im/Vakyansh/community?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

## Table of Contents

- [Eliminite direct RDBMS access](#eliminate_direct_rdbs_access)
  
## Eliminite direct RDBMS access

- Status: PROPOSED
- Deciders: [Rajat Singhal](https://github.com/srajat84), Pramod Verma, Vivek Raghavan
- Date: 2021-03-24

Technical Story/Epic: [Eliminiate direct RDBMS access](https://project-sunbird.atlassian.net/browse/SOC-1)

### Context and Problem Statement

Currently, the application service directly talks with RDBMS to fetch the sentences shown while doing Contributions and to fetch contributions while doing the Validations.

When the application will scale and more features are added, there may be some contention at RDBMS layer.

### Architecture change

- Add cache layer over RDMS for faster reads
- Write to topics asynchronously for faster writes
- Audio processing like 'automatic validation', 'SNR' etc can be done in 'contribution processor'
- Contribution processor can be in any other langauge like Python which is more suited fo audio processing
  
Options for cache : AWS ElasticCache - Redis
Options for queue: AWS Kafka, Redis, AWS SNS, AWS Kinesis

![ADR](img/crowdsource/adr1.png)

#### Positive Consequences

- RDBMS will be off loaded from frequent reads and writes
- Users will get better better performance
- System will be able to scale easily

#### Negative Consequences

- Overhead of maintaining cache and queue system
- The user experience will have to change for async behaviour of the system

### Decision Outcome

WIP
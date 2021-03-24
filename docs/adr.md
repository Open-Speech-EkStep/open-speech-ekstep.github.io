# Architecture Decision Records

## Table of Contents

- [Eliminiate direct RDBMS access](eliminate_direct_rdbs_access)
  
## Eliminiate direct RDBMS access

- Status: Proposed
- Drivers: Rajat Singhal
- Approvers: Pramod Verma, Vivek Raghavan
- Contributers: Rajat Singhal, Soujyo Sen, Heera Ballabh, Umair Manzoor
- Date: 2021-03-18

Technical Story: [ticket/issue URL]

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


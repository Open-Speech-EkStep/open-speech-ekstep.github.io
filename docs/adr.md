# Architecture Decision Records

## Caching layer above RDBMS

* Status: Proposed
* Drivers: Rajat Singhal
* Approvers: Pramod Verma, Vivek Raghavan
* Contributers: Rajat Singhal, Soujyo Sen, Heera Ballabh, Umair Manzoor
* Date: 2021-03-18

Technical Story: [ticket/issue URL]

### Context and Problem Statement

Currently, the application service directly talks with RDBMS to fetch the sentences shown while doing Contributions and to fetch contributions while doing the Validations.

The sentences and contributions are fetched in batches to avoid hitting the databse frequently. When the application will scale, there may be some contention at RDBMS layer.

To avoid that having a Cache above the RDBS layer is being considered

### Considered Options

### Decision Outcome

#### Positive Consequences

#### Negative Consequences

### Pros and Cons of the Options

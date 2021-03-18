# Caching layer above RDBMS

* Status: Proposed <!-- optional -->
* Drivers: Rajat Singhal<!-- optional -->
* Approvers: Pramod Verma, Vivek Raghavan <!-- optional -->
* Contributers: Rajat Singhal, Soujyo Sen, Heera Ballabh, Umair Manzoor <!-- optional -->
* Date: 2021-03-18 <!-- optional -->

Technical Story: [ticket/issue URL] <!-- optional -->

## Context and Problem Statement

Currently, the application service directly talks with RDBMS to fetch the sentences shown while doing Contributions and to fetch contributions while doing the Validations.

The sentences and contributions are fetched in batches to avoid hitting the databse frequently. When the application will scale, there may be some contention at RDBMS layer.

To avoid that having a Cache above the RDBS layer is being considered

## Considered Options

## Decision Outcome

### Positive Consequences

### Negative Consequences

## Pros and Cons of the Options

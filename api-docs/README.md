# Introduction

This section is where you can find our complete API reference, including request operations and responses.

## Environments

We offer access to **mainnets** and **testnets** through the following hosts/root endpoints (occasionally referred to as `customerApiDomain` in our legacy docs):

* `api.dfns.ninja` (testnet)
* `api.dfns.io` (mainnet)

⭑ **Note**: These environments are fully segregated — on two distinct TLDs. As such, entities and authorization can **NOT** be used cross-environment.

## API format

### HTTP headers

To ensure correct processing of API calls, please follow these conventions

| Header         | Value                 |
| -------------- | --------------------- |
| _Content-type_ | `application/json`    |
| _User-agent_   | `<must not be empty>` |


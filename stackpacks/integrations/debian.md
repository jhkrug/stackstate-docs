---
description: StackState curated integration
---

# Debian

## Overview

StackState Agent V2 will retrieve data from the Debian host it is running on and push this to StackState.

## Setup

### Prerequisites

To set up the StackState Debian integration, you need to have:

* [StackState Agent V2](../../setup/agent/linux.md) installed on a Debian host that is able to connect to StackState.
* The [StackState Agent V2 StackPack](agent.md) installed in StackState.

### Install

The Debian integration is part of the [StackState Agent V2 StackPack](agent.md).

## Data retrieved

StackState Agent V2 will synchronize the following data from the host it is running on with StackState:

* Hosts, processes and containers.
* Telemetry for hosts, processes and containers.
* For OS versions with a network tracer: 
  * Network connections between processes and containers.
  * Network traffic telemetry. 

See the [supported Linux versions](../../setup/agent/linux.md#supported-linux-versions) for details.

## See also

* [About the StackState Agent](../../setup/agent/about-stackstate-agent.md)
* [Deploy StackState Agent V2 on Linux](../../setup/agent/linux.md)

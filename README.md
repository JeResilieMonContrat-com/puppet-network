
# example42 puppet-network module

Example 42 Puppet module to manage networking on Linux and Solaris.

#### Table of Contents

1. [Description](#description)
2. [Setup - The basics of getting started with network](#setup)
    * [What network affects](#what-network-affects)
    * [Setup requirements](#setup-requirements)
    * [Beginning with network](#beginning-with-network)
3. [Usage - Configuration options and additional functionality](#usage)
4. [Reference - An under-the-hood peek at what the module is doing and how](#reference)
5. [Backwards compatibility](#backwards-compatibility)
6. [## Upgrade from version 3 and migration](#upgrade-from-version-3-and-migration)
6. [Limitations - OS compatibility, etc.](#limitations)
7. [Development - Guide for contributing to the module](#development)

## Description

This module configures networking on Linux and Solaris.

It manages hostname, interfaces, routes, rules and routing tables.

The new version (4) works only on Puppet 4 and later and has several changes in class and defines parameters.

Options to provide [backwards compatibility](#backwards-compatibility) are available in order to use the legacy versions of the module's defines.

## Module Description

Main class is used as entrypoint for general variables and wrapper for Hiera driven management of the provided defines.

Classes:

- network - Allows Hiera driven configuration of the various defines
- network::hostname - Manages the system hostname

Defines:

- network::interface - Manages network interfaces
- network::route - Manages network routes
- network::routing_table - Manages iproute2 routing tables
- network::rule - Manages network rules
- network::netplan - Generic netplan.io configuration
- network::netplan::interface - Netplan.io interface configuration

Legacy defines (inherited from version 3 of the module):

- network::legacy::interface - Manages network interfaces
- network::legacy::route - Manages network routes
- network::legacy::mroute - Manages network routes in an alternative, easier to handle, way
- network::legacy::routing_table - Manages iproute2 routing tables
- network::legacy::rule - Manages network rules

## Setup


### What puppet-network affects

The main network class does nothing with default values for parameters but can be included and used
as entrypoints to manage via Hiera hashes of the defines provided in the modules.

Single defines manage the relevant network entity (interfaces, routes, rules, tables ...)

### Setup Requirements

Puppetlabs-stdlib module is the only prerequisite module.

Puppet 4 or later is required for this module.

If you have earlier Puppet versions use code from the 3.x tags.

### Beginning with network

Include the main class to be able to manage via Hiera the network resources handled by the module:

    include network
    
This allows to configure network resources with Hiera data like:

    network::hostname: server.example.com
    network::interfaces_hash:
      eth0:
        enable_dhcp: true
      eth1:
        ipaddress: '10.42.42.50'
        netmask: '255.255.255.0'
    network::routes_hash:
      eth1:
        routes:
          99.99.228.0/24: eth0
          100.100.244.0/22: 174.136.107.1
          101.99.228.0/24: 'eth0 table 1'
            
## Usage


## Reference

For full reference look at the defines documentation.

For configuration examples via Hiera look at the examples directory.

## Upgrade from version 3 and migration

When upgrading from version 3 to version 4 of this module you have 2 options:

- Keep on using the old defines with relevant data
- Migrate to the new defines

### Keep on using old defines

The Version 3 defines for network resources have been renamed to legacy but they entrypoint have been preserved, so, if you managed your network cnfigurations via the network::*_hash Hiera keys you should not have any change in behaviour.

| Version 3 defines | Version 4 equivalent | Hiera entrypoint |
| network::interface | network::legacy::interface | network::interfaces_hash | 
| network::route | network::legacy::route | network::routes_hash |
| network::mroute | network::legacy::mroute | network::mroutes_hash |
| network::rule | network::legacy::rule | network::rules_hash | 
| network::routing_table | network::legacy::routing_table | network::tables_hash |

If you use in you profile, classes or wrapper defines directly the above version 3 defines, then you need to rename them using the legacy names.

Given the quite critical nature of the resources managed we highly recommend to test carefully the effect of an upgrade of
this module on your current infrastructure and to keep the first runs on noop mode.

Some configuration files might change as well, in minor details like new lines or spaces, even when using the legacy defines. To avoid automatic restart of network service on a configuration change set:

    network::config_file_notify: false

### Miggrate to the new defines

The new, version 4 defines have replaced the names of the old ones and can be configured via hiera using new entrypoints in the netwrok class:

| Version 4 defines | Hiera entrypoint |
| network::interface | network::interfaces | 
| network::route | network::routes |
| network::mroute | network::mroutes |
| network::rule | network::rules | 
| network::routing_table | network::tables |

The parameters of these defines have changes and, in many cases, also their internals.

## Limitations

This module currently supports only the major Linux distributions (RedHat and derivatives, Debian and derivatives, included Cumulus, SuSE
and derivatives, Solaris).

The legacy defines are introduced for backwards compatibility only and are not supposed to be improved in the future.
The new, default, defines, are designed in a way to be more easily adaptable to custom needs (for example there's no need to add parameters
for any new or uncommon configuration entry).

## Development

To contribute to the module submit a Pull Request on GitHub.

Please be sure to provide:

- Code changes for syntax and lint
- Relevant documentation
- Relevant spec tests



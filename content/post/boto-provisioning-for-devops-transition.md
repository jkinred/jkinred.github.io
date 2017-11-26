---
title: "Python boto provisioning for a transitioning DevOps team"
date: 2017-11-26T07:49:29+11:00
draft: false
---

In this post I describe using Python boto to provision resources in AWS for a
company which was transitioning from a traditional ITIL model with separate
Engineering and Operations teams to a more modern "DevOps" way of working.

The Operations team is skilled at running servers in private datacentres and
will now be responsible for a platform running on AWS.

Primarily, the provisioning scripts were written as a middle ground between
modern "infrastructure as code" and accommodating an operational team which is
jumping through about 8 years of technical evolution in only a few months.

In this particular organisation it is super important to take a patient and
pragmatic approach. Focusing on people and striking the right balance with
technology is the key to achieving results.

_**Note:** This blog post was written retrospectively and this provisioning
system was subsequently and successfully replaced by Terraform._

# Just show me the code!

Code for this article can be found in a private repository at
[jkinred/midops-provisioning-scripts](https://bitbucket.org/jkinred/midops-provisioning-scripts).
Access will be granted on request, please find my email address in the Contact
section.

# Overview

The scripts provision the infrastructure for a hosted Content Management System
(CMS) built around Adobe CQ, a Java application.

Several customers are signed up and complete customer separation must be
ensured due to operational constraints as well as the future ability to "hand
off" an individual customer CMS to a third party.

# Requirements

* A central definition describing a complete customer platform must exist
* Each customer definition must be kept separate from any other customer definition
* The starting point for the scripts is an empty AWS account containing a single "bootstrap" user
* The scripts must be able to operate idempotently or audit the running infrastructure against the central definition
* Instances must start and hand-off control to cloud-init and subsequently configuration management for final provisioning
* The scripts and definitions must be usable by operators with minimal skill in modern tooling and platforms

Initially, the system must be able to manage the following AWS resources:

* IAM users, groups and policies
* VPC
* Subnets
* EBS storage volumes
* EC2 compute
* Elastic IPs
* Route53 records

# Existing software

Terraform was evaluated and we determined that it didn't match the unique
business constraints and was also considered to be too immature, we lovingly
nicknamed it *Terrorform*.

I submitted these pull requests to fix an issue I experienced during evaluation:

* [Allow setting of the EbsOptimized flag when provisioning instances](https://github.com/mitchellh/goamz/pull/94)
* [Allow building of EC2 instances with ebs_optimized flag](https://github.com/hashicorp/terraform/pull/260)

CloudFormation was also evaluated. The JSON made my eyes bleed and it was even
scarier to run than Terraform.

# Design

The provisioning system will be based on a collection of definitions which
describe each AWS resource. Taken as a whole, the definitions will describe the
entire platform.

Definitions will be written in YAML for usability and data portability should
another tool become available.

The YAML definitions will be read by scripts written in Python using the boto
AWS library.

The YAML definitions will be stored in a Git repository allowing for backup,
version control and auditing.

### Uniquely identifying each resource

Each resource will be assigned a generated UUID which will be applied to the
AWS resource using tags. This will allow the scripts to locate a resource
accurately when performing updates or auditing.

In addition, a unique identifier allows for an entire platform to be defined
and self-referenced before any resources exist.

### Standard definition entries

Each definition will include the following standard entries:

    TODO

### Full definition

This example shows the full definition of an _instance_ resource. See the
`examples/` directory for definitions of other resource types.

    TODO

# Usage

This section only serves as a guide, please refer and contribute to the
platform documentation and `--help` for full details.

It is a pre-requisite that appropriate developer tooling is configured. Those
starting out can use the DevOps SOE Vagrant box which comes preconfigured with
all the right tools.

#### Setup the provisioning scripts

    mkvirtualenv midops-provisioning-scripts
    git checkout https://git.companyname.com/git/midops-provisioning-scripts
    cd midops-provisioning-scripts
    pip install -r requirements.txt

#### Checkout the definitions for a customer

    git checkout https://git.company.com/git/customer-platform-definitions

#### Provision a single resource

    cd customer-platform-definitions
    midops-provisioning provision volumes/snowflake01-data.yaml
    midops-provisioning provision instances/snowflake01.yaml

#### Provision all the resources

Just use shell-fu to provision the resources you need.

Example:

    for volume in volumes/web*; do midops-provisioning provision $volume; done
    for instance in instances/web*; do midops-provisioning provision $instance; done

# Contributing

To contribute to the provisioning tools, please create a feature branch and
submit a pull request as per the standard process. The CI job can be found in
Bamboo [here](https://ci.companyname.com/BUILD-ID). New builds are created for
branches automatically, please make sure the tests pass before submitting a PR.

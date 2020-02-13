> This is the source code and project. For the PagerDuty Agent Install Guide,
> see http://www.pagerduty.com/docs/guides/agent-install-guide/

# Introduction

The PagerDuty Agent is a program that lets you easily integrate your monitoring
system with PagerDuty.

It includes command-line tools to trigger, acknowledge & resolve PagerDuty
incidents.

The supported events are those listed in the PagerDuty Integration API:

> <https://v2.developer.pagerduty.com/docs/events-api>

The PagerDuty Agent is completely open-source which means that you can download
the source code and customize it for your needs.

The Agent requires Python 2.6 or 2.7. The instructions here assume that you're
on a Mac.

## Developing

### Running in Development

#### Locally

You can run the Agent in development without any setup. Start the Agent daemon
as follows:

`bin/pdagentd.py`

When run in development the daemon automatically creates a `tmp` directory
inside the project where it stores its various work files.

Similarly, you can use the `pd-send` command immediately.

```
bin/pd-send -h
usage: pd-send [-h] -k SERVICE_KEY -t {trigger,acknowledge,resolve}
               [-d DESCRIPTION] [-i INCIDENT_KEY] [-f FIELDS]

Queue up a trigger, acknowledge, or resolve event to PagerDuty.
...
```

Make sure that you have run the daemon at least once so that the `tmp`
directory exists.

You can stop the daemon as follows:

`kill $(cat tmp/pdagentd.pid)`

#### With Docker

To run the Agent in a production-like environment, use Docker. We currently have two supported operating systems: Ubuntu 16.04 and CentOS 7. With Docker installed, run `./scripts/run-console.sh <ubuntu>` or `./scripts/run-console.sh <centos>` to spin up the Docker container, run the Agent, and drop into a console. 

Once in the console, you can send events via `pd-send`.

### IDE Setup

For IDE setup instructions see [PyDev Setup](pydev-setup.md) or [IDEA Setup](idea-setup.md). Apart from the usual benefits, the IDEs provide PEP-8 warnings which we care about.

### Build Tools

To perform a complete automated build, you'll need to install Vagrant and Scons.  See [Vagrant Setup](vagrant-setup.md) and [Scons Setup](scons-setup.md) for instructions.

### Running Unit Tests

You can run the unit tests with the following command:

`scons test-local`

To run them without installing SCons, use the `run-tests.py` test runner, e.g.:

`python run-tests.py unit_tests/test_*.py unit_tests/thirdparty/test_*.py`

### Running Integration Tests

You can run the integration tests with the following command:

`./scripts/full-integration-test.sh <centos or ubuntu>`

Make sure to run tests with both `centos` and `ubuntu` options, as those are the supported Linux distributions for `pdagent`.


### Building Packages

For development builds, you can perform a full automated clean build of the
Agent with the following steps:

1. Configure signing keys by following the [One-time setup of GPG keys](build-linux/howto.md#one-time-setup-of-gpg-keys) instructions.


2. Run the following commands:
```
make ubuntu
make centos
```

3. Run integration tests on the packages as follows:
  * Edit the file `pdagenttestinteg/util.sh` and change the line `SVC_KEY=CHANGEME` to a real PagerDuty Service API Key in your pdt test account.
  * Run the command:
```
scons test-integration
```

This will run the integration tests on the various VMs using the packagesbuilt in the previous step. Note that the tests will trigger and resolve some incidents when they run.

If you want to build packages by hand, follow the instructions in the
[Build Linux Howto](build-linux/howto.md).

Similarly, you can check the SCons targets using `scons -h` for instructions on
performing specific builds tasks and on specific VMs.

#### Some helpful scripts

```
scripts/rev_pkgs.sh
```

This will remove the installed pdagent packages from the vagrant build machines, `agent-minimal-centos65` and `agent-minimal-ubuntu1204`, and run `scons local-repo gpg-home=build-linux/gnupg` to install them again.  Run this anytime you revise a package artifact like `build-linux/deb/postinst`.

```
scripts/kill_pids.sh
```

This will kill stray pdagent processes and cleanup the pidfile on all vagrant machines.  Run this
if your changes are causing integration tests to fail due to improper process managment via `service` or `systemctl`.

```
scripts/setup_upgrade_test.sh
```

This will vagrant destroy, up and install the latest public repo pdagent package on machines for upgrade testing via `scons test-integration`.

#License
Copyright (c) 2013-2014, PagerDuty, Inc. <info@pagerduty.com>
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

  * Redistributions of source code must retain the above copyright
    notice, this list of conditions and the following disclaimer.
  * Redistributions in binary form must reproduce the above copyright
    notice, this list of conditions and the following disclaimer in the
    documentation and/or other materials provided with the distribution.
  * Neither the name of the copyright holder nor the
    names of its contributors may be used to endorse or promote products
    derived from this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE
LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
POSSIBILITY OF SUCH DAMAGE.

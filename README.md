# Mattermost for OpenShift Origin 3

This is instant Mattermost application for OpenShift Origin 3.

The license applies to all files inside this repository, not Mattermost itself.

## Prerequisites

OpenShift Origin 3 up and running, including the capability to create a new project. The simple way is to use `oc cluster up` or [Minishift](https://docs.openshift.org/latest/minishift/getting-started/installing.html)

And you need to deploy MySQL, described below.

## Disclaimer

By now only a Docker build strategy based Mattermost application is provided, this may not be usable on OpenShift Online 3.

This template and Mattermost startup script `mattermost-launch.sh` only supports MySQL.

Support for this work is provided as 'best can do' via GitHub.

## Installation

### Configuration

First of all, lets create a project for mattermost: `oc new-project mattermost`

We will use a [ServiceAccount](https://docs.openshift.com/container-platform/3.7/dev_guide/service_accounts.html) to run Mattermost, this account will have access to the database [Secrets](https://docs.openshift.com/container-platform/3.7/dev_guide/secrets.html):

```
oc create --filename mattermost.yaml # to import the template
oc create serviceaccount mattermost # our deployment will use this
oc create secret generic mattermost-database --from-literal=user=mmuser --from-literal=password=mostest
oc secrets link mattermost mattermost-database # make the secret available to the serviceaccount
```

### Deployment

As Mattermost depends on it, lets deploy Postgres...

Next step, import the current image from quay.io and tag it as latest:

```
oc import-image registry.centos.org/mattermost/mattermost-team:4.7.1 --confirm
oc tag mattermost-team:4.7.1 mattermost-team:latest
```

If you build your own image dont forget to push it to OpenShift's ImageStreamTag `mattermost/mattermost-team:latest`.

Main step: deploy Mattermost app using the provided template: `oc new-app mattermost --labels=app=mattermost`. Deployments and Services will be created for you.

And a route:

`oc expose service/mattermost --labels=app=mattermost --hostname=mattermost.example.com`

## Usage

Point your browser at `mattermost.example.com`, the first user you create will
be an Administrator of Mattermost.

## Updates

If a new Mattermost container image is available, or if you build one yourself, you need to import it to the ImageStream and retag latest to it. This will automatically deploy the new version.

## Building

The actual mattermost-team container image used is build by the CentOS Container Index, have a look at: https://registry.centos.org/mattermost/mattermost-team

## Copyright

Copyright (C) 2016-2018 Red Hat Inc.

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program. If not, see <http://www.gnu.org/licenses/>.

The GNU General Public License is provided within the file LICENSE.

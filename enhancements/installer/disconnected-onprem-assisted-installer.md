---
title: assisted-installer-disconnected
authors:
  - "@mhrivnak"
reviewers:
  - TBD
approvers:
  - TBD
creation-date: 2020-10-08
last-updated: 2020-10-26
status: provisional|implementable|implemented|deferred|rejected|withdrawn|replaced
see-also:
  - "/enhancements/installer/connected-assisted-installer.md"  
  - "/enhancements/installer/connected-onprem-assisted-installer.md"  
---

# Assisted Installer On-Prem Disconnected

## Release Signoff Checklist

- [ ] Enhancement is `implementable`
- [ ] Design details are appropriately documented from clear requirements
- [ ] Test plan is defined
- [ ] Graduation criteria for dev preview, tech preview, GA
- [ ] User-facing documentation is created in [openshift-docs](https://github.com/openshift/openshift-docs/)

## Summary

Extending the ability to [run the assisted-installer on-premise in a connected
environment](connected-onprem-assisted-installer.md), this enhancement proposes
to run the assisted-installer in much the same way but in a disconnected
environment. Doing so requires making an RHCOS ISO and several container images
available locally, as well as configuring assisted-installer to use a local
container registry.

## Motivation

Many OpenShift users, particularly those wishing to deploy on bare metal,
maintain networks that do not have access to the internet.

### Goals

* Enable users to deploy a single cluster with the assisted-installer inside a
  disconnected network environment.

### Non-Goals

* Providing a local container registry. That will be the user's responsibility.

## Proposal

As previously described in the connected on-premise enhancement, a user will
visit cloud.redhat.com to download a live ISO with which they can run the
assisted installer on-premise. The RHCOS-based ISO will include:

* the user's pull secret
* a systemd unit file that runs all containers necessary to run the assisted installer

For disconnected environments, it will additionally include:

* Container image references that use the local container registry instead of Red Hat's.
* Public CA certificate to use in verifying the connection to the local container registry.
* Credentials, if any, to access the local container registry.
* Proxy information as needed.

Upon submitting the above information, the user will be shown instructions for
mirroring the required container images, in addition to a link from which they
can download their customized RHCOS live ISO.

### User Stories

#### Story 1

I want to install OpenShift using the assisted installer running inside my
disconnected or air-gapped network environment.

#### Story 2

I want to install OpenShift using the assisted installer running on-premise,
and I want to mirror all required content locally. Mirroring gives me control
of the content, reduces bandwidth consumption, and/or decreases the amount of
time it takes to provision a cluster.

### Implementation Details/Notes/Constraints [optional]

#### Mirroring

Several container images need to be mirrored into a local registry. When a user
is given a link to download their on-premise disconnected live ISO, they will
also be given a set of commands utilizing `oc image mirror` to mirror required
content. The registry information they provide for the live ISO can be used to
customize the `oc image mirror` commands such that they can be copied and
pasted.

The set of commands provided should also include one that mirrors the
appropriate OpenShift release using `oc adm release mirror`.

### Risks and Mitigations

We will be asking users to enter credentials for their local registry into
cloud.redhat.com's web UI, so that we can embed those credentials into the ISO
they download. Some users may not be willing to share their credentals due to
security implications or company policy. We may need to provide a way for them
to add their credentials after downloading the ISO.

## Design Details

### Open Questions

#### Locally create ISO?

Users who do not want to share details of their local registry, particularly
authentication credentials, may not want to use cloud.redhat.com to generate
their disconnected on-premise ISO. If that's the case, we may need to provide
a way to add those details after they download the ISO.

### Test Plan

**Note:** *Section not required until targeted at a release.*


### Graduation Criteria

**Note:** *Section not required until targeted at a release.*


### Upgrade / Downgrade Strategy

Since the assisted-installer on-premise will be one-time-use, there is no need
to upgrade it.

### Version Skew Strategy

There is no change here compared to running the assisted installer in a
connected environment.

## Implementation History


## Drawbacks


## Alternatives

### Registry

Expecting the user to provide their own local registry is a significant
requirement. We could consider providing a light-weight registry for them to
use, perhaps even pre-populated with required images. Though it is a standard
practice with OpenShift to expect that users with disconnected network
environments run their own container image registry.

### oc mirror command

We could extend the `oc adm release mirror` command to optionally include the
assisted-installer container images, rather than provide the user with
individual `oc image mirror` commands. However that approach would make more
sense after assisted-installer becomes more closely tied to the OpenShift
release payload.

## Infrastructure Needed [optional]

None.

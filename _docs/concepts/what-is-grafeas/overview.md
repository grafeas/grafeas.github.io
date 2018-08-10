---
title: Overview
overview: Provides a conceptual introduction to Grafeas, including the problems it solves and its high-level architecture.

order: 15

layout: docs
type: markdown
---

# Grafeas: A Component Metadata API

Grafeas defines an API spec for managing metadata about software artifacts, such
as VM images, jar files, scripts, and so on. You can use Grafeas to define and
aggregate information about your project's components.

Grafeas divides metadata information into [_notes_](#notes), which contain
high-level information, and [_occurrences_](#occurrences), which contain
information about how and when notes occur in a particular project. This
division allows third party metadata providers to create and manage metadata on
behalf of many customers. It also allows for fine-grained access control of
different types of metadata.

## Definition of terms

### Notes

A _note_ describes a high-level piece of metadata. Notes are generally found via
analysis, and are things that occur multiple times across different projects.
For example, you could create a note about a particular vulnerability after
analyzing a Linux package. You would also use a note to store information about
the builder of a build process. Notes are often owned and created by providers
who analyze users' packages.

Note names should follow the format `/projects/<project_id>/notes/<note_id>`.
The note id needs to be unique per note-project, and informative if possible.
It's generally preferable to store notes and occurrences in separate projects,
allowing for more fine-grained access control.

Notes should be editable only by the note owner, and read-only for users who
have access to occurrences referencing them.

### Occurrences

An _occurrence_ is essentially an instantiation of a note. Occurrences describe
project-specific details of a given note. For example, an occurrence of a note
about a vulnerability would describe the package that the vulnerability was
found in, specific remediation steps, and so on. Alternatively, an occurrence of
a note about build details would describe the container images that resulted
from a build.

Occurrence names should follow the format
`/projects/<project_id>/occurrences/<occurrence_id>`. The occurrence id must be
unique per occurrence-project and is often random. Typically, occurrences are
stored in separate projects than the ones where notes are created.

Write access to occurrences should only be granted to users who have access to
link a note to the occurrence. Any users can have read access to occurrences.

### Resource URLs

A _resource URL_ is a unique URL for the image or the container to which a given
occurrence applies. Component resource URLs should be both unique per resource
and immutable. This ensures that each occurrence is always associated with
exactly one component. If using resources that cannot be made immutable, you
should append a timestamp. Where possible, prefer content addressable resource
URLs.

The following table provides examples of possible resource URLs for several
component types:

Component Type | Identifier                               | Example
-------------- | ---------------------------------------- | -------
Debian         | deb://dist(optional):arch:name:version   | deb://lucid:i386:acl:2.2.49-2
Docker         | https://Namespace/name@sha256:<Checksum> | https://gcr.io/scanning-customer/dockerimage@sha256:244fd47e07d1004f0aed9c156aa09083c82bf8944eceb67c946ff7430510a77b
Generic file   | file://sha256:<Checksum>:name            | file://sha256:244fd47e07d1004f0aed9c156aa09083c82bf8944eceb67c946ff7430510a77b:foo.jar
Maven          | gav://group:artifact:version             | `gav://ant:ant:1.6.5`
NPM            | npm://package:version                    | npm://mocha:2.4.5
NuGet          | nuget://module:version                   | nuget://log4net:9.0.1
Python         | pip://package:version                    | pip://raven:5.13.0
RPM            | rpm://dist(optional):arch:name:version   | rpm://el6:i386:ImageMagick:6.7.2.7-4

## Kind Specific Schemas

Each kind of metadata information has a strict schema. This allows you to
normalize data from multiple providers, making it easier to see meaningful
insights about your components over time. Defining different kinds of data
also makes it easy to expand Grafeas to support new metadata types.

The currently supported kinds are defined below, along with a brief summary of
the type of information each kind of note and occurrence contains.

|Kind                 |Note Summary                                   |Occurrence Summary     |
|---------------------|-----------------------------------------------|-----------------------|
|ATTESTATION          |A logical attestation role or authority, used as an anchor for specific attestations|An attestation by an authority for a specific property and resource|
|BUILD_DETAILS        |Builder version and signature                  |Details of this specific build, such as inputs and outputs|
|DEPLOYMENT_HISTORY   |A resource that can be deployed                |Details of each deployment of the resource|
|DISCOVERY            |Only used as an anchor for specific occurrences|Information about the status of an image after the first scan, such as package vulnerability, base image, and package manager info|
|IMAGE_BASIS          |Information about the base image of a container|Information about layers included on top of the base image in a particular container|
|PACKAGE_MANAGER      |Package descriptions                           |Filesystem locations detailing where the package is installed in a specific resource|
|PACKAGE_VULNERABILITY|CVE or vulnerability description and details including severity, versions|Affected packages/versions in a specific resource|

## Next steps
You can run Grafeas locally following [these
instructions](docs/running_grafeas.md). Once you have a running server, you can
use the [client libraries](https://github.com/grafeas) to experiment with
creating notes and occurrences in Grafeas. There are client libraries available
in Java, Go, Ruby, and Python.

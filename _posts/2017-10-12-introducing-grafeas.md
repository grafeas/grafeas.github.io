---
title: "Introducing Grafeas: An open-source API to audit and govern your software supply chain"
overview: Grafeas announcement
published: false
permalink: blog/introducing-grafeas
attribution: The Grafeas Team
layout: post
type: markdown
redirect_from: "/blog/grafeas-announcement.html"
---

Building software at scale requires strong governance of the software supply chain, and strong governance requires good data. Today, Google, JFrog, Red Hat, IBM, Black Duck, Twistlock, Aqua Security, and CoreOS are pleased to announce Grafeas, an open source initiative to define a uniform way for auditing and governing the modern software supply chain. Grafeas (“scribe” in Greek) provides organizations with a central source of truth for tracking and enforcing policies across an ever growing set of software development teams and pipelines. Build, auditing, and compliance tools can use the Grafeas API to store, query, and retrieve comprehensive metadata on software components of all kinds.

As part of Grafeas, Google is also introducing Kritis, a Kubernetes policy engine that helps customers enforce more secure software supply chain policies. Kritis (“judge” in Greek) enables organizations to do real-time enforcement of container properties at deploy time for Kubernetes clusters based on attestations of container image properties (e.g., build provenance and test status) stored in Grafeas.

<!--end_excerpt-->

“Shopify was looking for a comprehensive way to track and govern all the containers we ship to production,” said Jonathan Pulsifer, Senior Security Engineer at Shopify. “We ship over 6,000 builds every weekday and maintain a registry with over 330,000 container images. By integrating Grafeas and Kritis into our Kubernetes pipeline, we are now able to automatically store vulnerability and build information about every container image that we create and strictly enforce a built-by-Shopify policy: our Kubernetes clusters only run images signed by our builder. Grafeas and Kritis actually help us achieve better security while letting developers focus on their code. We look forward to more companies integrating with the Grafeas and Kritis projects.” (Read more in Shopify’s [blog post](https://engineering.shopify.com/blogs/engineering/how-shopify-governs-containers-at-scale-with-grafeas-and-kritis).)

## The challenge of governance at scale 

Securing the modern software supply chain is a daunting task for organizations both large and small, exacerbated by several trends:

* **Growing, fragmented toolsets**: As an organization grows in size and scope, it tends to use more development languages and tools, making it difficult to maintain visibility and control of its development lifecycle.
* **Open-source software adoption**: While open-source software makes developers more productive, it also complicates auditing and governance.
* **Decentralization and continuous delivery**: The move to decentralize engineering and ship software continuously (e.g., “push on green”) accelerates development velocity, but makes it difficult to follow best practices and standards.
* **Hybrid cloud deployments**: Enterprises increasingly use a mix of on-premises, private, and public cloud clusters to get the best of each world, but find it hard to maintain 360-degree visibility into operations across such diverse environments.
* **Microservice architectures**: As organizations break down large systems into container-based microservices, it becomes harder to track all the pieces.

As a result,  organizations generate vast quantities of metadata, all in different formats from different vendors and are stored in many different places. Without uniform metadata schemas or a central source of truth, CIOs struggle to govern their software supply chains, let alone answer foundational questions like: “Is software component X deployed right now?” “Did all components deployed to production pass required compliance tests?” and “Does vulnerability Y affect any production code?”

## The Grafeas approach

Grafeas offers a central, structured knowledge-base of the critical metadata organizations need to successfully manage their software supply chains. It reflects best practices Google has learned building internal security and governance solutions across millions of releases and billions of containers. These include:
 
* **Using immutable infrastructure** (e.g., containers) to establish preventative security postures against persistent advanced threats
* **Building security controls into the software supply chain**, based on comprehensive component metadata and security attestations, to protect production deployments
* **Keeping the system flexible** and ensuring interoperability of developer tools around common specifications and open-source software

Grafeas is designed from the ground up to help organizations apply these best practices in modern software development environments, using the following features and design points:

* **Universal coverage**: Grafeas stores structured metadata against the software component’s unique identifier (e.g., container image digest), so you don’t have to co-locate it with the component’s registry, and so it can store metadata about components from many different repositories.
* **Hybrid cloud-friendly**: Just as you can use JFrog Artifactory as the central, universal component repository across hybrid cloud deployments, you can use the Grafeas API as a central, universal metadata store.
* **Pluggable**: Grafeas makes it easy to add new metadata producers and consumers (for example, if you decide to add or change security scanners, add new build systems, etc.).
* **Structured**: Structured metadata schemas for common metadata types (e.g., vulnerability, build, attestation, and package index metadata) let you add new metadata types and providers, and the tools that depend on Grafeas can immediately understand those new sources.
* **Strong access controls**: Grafeas allows you to carefully control access for multiple metadata producers and consumers.
* **Rich query-ability**: With Grafeas, you can easily query all metadata across all of your components so you don’t have to parse monolithic reports on each component.

## Defragmenting and centralizing metadata

At each stage of the software supply chain (code, build, test, deploy, and operate), different tools generate metadata about various software components. Examples include the identity of the developer, when the code was checked in and built, what vulnerabilities were detected, what tests were passed or failed, and so on.This metadata is then captured by Grafeas. See the image below for a use case of how Grafeas can provide visibility for software development, test, and operations teams as well as CIOs. 

<figure>
    <a href="{{home}}/img/grafeas-example-flow.png">
    <img style="max-width: 100%;" src="{{home}}/img/grafeas-example-flow.png" alt="Example of storing/querying/retrieving artifact metadata from Grafeas" title="Grafeas Example Flow" />
    </a>
</figure>

To give a comprehensive, unified view of this metadata, we built Grafeas to promote cross-vendor collaboration and compatibility; we’ve released it as open source, and are working with contributors from across the ecosystem to further develop the platform:

* **JFrog** is implementing Grafeas in the JFrog Xray API and will support hybrid cloud workflows that require metadata in one environment (e.g., on-premises in Xray) to be used elsewhere (e.g., on Google Cloud Platform). Read more on [JFrog’s blog](https://www.jfrog.com/blog/google-and-jfrog-announce-grafeas/).
* **Red Hat** is planning on enhancing the security features and automation of Red Hat Enterprise Linux container technologies in OpenShift with Grafeas. Read more on [Red Hat’s blog](https://www.redhat.com/en/blog/red-hat-google-cloud-and-other-industry-leaders-join-together-standardize-kubernetes-service-component-auditing-and-policy-enforcement).
* **IBM** plans to deliver Grafeas and Kristis as part of the IBM Container Service on IBM Cloud, and to integrate our Vulnerability Advisor and DevOps tools with the Grafeas API. Read more on [IBM’s blog](https://developer.ibm.com/dwblog/2017/grafeas/).
* **Black Duck** is collaborating with Google to implement the Google artifact metadata API implementation of Grafeas, to bring improved enterprise-grade open-source security to Google Container Registry and Google Container Engine. Read more on [Black Duck’s blog](https://blog.blackducksoftware.com/black-duck-google-grafeas).
* **Twistlock** will integrate with Grafeas to publish detailed vulnerability and compliance data directly into orchestration tooling, giving customers more insight and confidence about their container operations. Read more on [Twistlock’s blog](https://www.twistlock.com/2017/10/12/twistlock-google-grafeas/).
* **Aqua Security** will integrate with Grafeas to publish vulnerabilities and violations, and to enforce runtime security policies based on component metadata information. Read more on [Aqua’s blog](https://blog.aquasec.com/governance-and-control-for-the-container-supply-chain-using-aqua-security-and-google-grafeas).
* **CoreOS** is exploring integrations between Grafeas and Tectonic, its enterprise Kubernetes platform, allowing it to extend its image security scanning and application lifecycle governance capabilities.

Already, several contributors are planning upcoming Grafeas releases and integrations:

* JFrog’s Xray implementation of Grafeas API
* A Google artifact metadata API implementation of Grafeas, together with Google Container Registry vulnerability scanning
* Bi-directional metadata sync between JFrog Xray and the Google artifact metadata API
* Black Duck integration with Grafeas and the Google artifact metadata API

Building on this momentum, we expect numerous other contributions to the Grafeas project early in 2018. 

## Join us!

The way we build and deploy software is undergoing fundamental changes. If scaled organizations are to reap the benefits of containers, microservices, open source and hybrid cloud, they need a strong governance layer to underpin their software development processes. Here are some ways you can learn more about and contribute to the project:

* Register for the [JFrog-Google webinar](https://leap.jfrog.com/WN2017-ImplementingaSingleSourceofTruthinaHybridCloudWorld_RegistrationPage.html)
* Try Grafeas now and join the GitHub project: [https://github.com/grafeas](https://github.com/grafeas)
* Attend Shopify’s talks at [Google Cloud Summit in Toronto](https://cloudplatformonline.com/summit-toronto-2017-schedule.html) on 10/17 and [KubeCon](https://kccncna17.sched.com/event/CU83/securing-shopifys-paas-on-gke-i-jonathan-pulsifer-shopify) in December
* Fill out [this form](https://docs.google.com/forms/d/e/1FAIpQLSdr8kDTkAkml5f9TW_kzz06C0s0QuV_sWYzHC7NM90F5CZ2bQ/viewform) to learn more about upcoming releases or talk to us about integrations
* [Sign up](https://groups.google.com/forum/#!forum/grafeas-users) for the Grafeas discussion group, grafeas-users@googlegroups.com
* See [grafeas.io](http://grafeas.io) for documentation and examples

We hope you will join us!

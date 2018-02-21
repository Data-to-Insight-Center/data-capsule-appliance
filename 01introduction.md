---
layout: page
title: Introduction
permalink: /intro/
---
The HTRC Data Capsule (DC) service is a secure computing environment for interactive analysis of restricted content. Users are provisioned with a Capsule (a virtual machine) through which they install tools to interact with restricted content (e.g., HathiTrust volumes). Users work within their Capsule. Exports from the Capsule are reviewed by curators. This prevents users from exporting restricted content from their Capsule in accordance with the Non-consumptive Use Research Policy.

HTRC Data Capsule service has two modes of operation:
- **Maintenance Mode** - the default mode when the capsule is launched. In this mode user
can access the internet, but not secure services. This mode is for downloading any necessary software packages and for preparing the environment for analysis.
- **Secure Mode** - in this mode the access to the internet is completely cut off and  secure services are enabled.

HTRC Data Capsule service consists of the following:
- **Data Capsule Backend** - this is a qemu-based virtualization system that provides virtual machines (Data Capsules). It uses bash scripts to create and manage data capsules.
- **Data Capsule API** - this is a REST API that triggers backend scripts to create and manage data capsules.

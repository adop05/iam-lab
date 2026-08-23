# Enterprise Identity & Access Management (IAM) Lab

## Executive Summary
This project demonstrates the end-to-end engineering of a modern Zero-Trust architecture. It bridges the gap between IT infrastructure and cybersecurity operations by deploying a centralized directory backend, configuring a Single Sign-On (SSO) security gateway with Multi-Factor Authentication (MFA), and aggregating authentication telemetry into an enterprise SIEM for threat detection.

## Architecture & Technologies
*   **Hypervisor & Networking:** Proxmox VE, pfSense, Tailscale (Mesh VPN)
*   **Infrastructure as Code (IaC):** Terraform, Cloud-Init
*   **Configuration Management:** Ansible
*   **Identity Services:** 389 Directory Server (LDAP), Keycloak (SSO/IdP)
*   **Security & Telemetry:** Splunk Enterprise, Splunk Universal Forwarder, Firewalld

---

## Phase 1: Enterprise Directory Services

**Objective:** 
Establish a centralized identity database to serve as the backend for an enterprise Single Sign-On (SSO) environment.

**Engineering Execution:**
*   **Automated Provisioning:** Utilized a pre-existing Infrastructure-as-Code pipeline to provision a dedicated, lightweight Fedora node within a segmented Proxmox virtual environment.
*   **Idempotent Configuration:** Engineered an Ansible playbook to automate the non-interactive deployment of **389 Directory Server**. The playbook handles package dependencies, generates the silent `.inf` answer file, and provisions the initial database structure seamlessly.
*   **Host-Based Security:** Integrated `firewalld` management directly into the Ansible pipeline, ensuring the host rigidly drops all traffic except explicit LDAP (TCP/389) and LDAPS (TCP/636) connections before the service even boots. 
*   **Network Segmentation Validation:** Verified the strict network isolation of the directory server by attempting external connections, successfully confirming that pfSense drops unrouted external traffic. Validated directory health by executing `ldapsearch` queries strictly from an internal, authorized jumpbox.

**Validation Command:**
> `ldapsearch -x -H ldap://10.0.0.106 -b "dc=lab,dc=local" -D "cn=Directory Manager" -w "[REDACTED]"`

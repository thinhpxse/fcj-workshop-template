---
title: "Blog 3"
weight: 1
chapter: false
pre: " <b> 3.3 </b> "
---
{{% notice warning %}}
⚠️ **Note:** The information below is for reference only. Please **do not copy it verbatim** into your official report, including this section.
{{% /notice %}}

# Electronic Arts Optimizes Game Update Processes with AWS

The game patching process has always been a major challenge: game sizes can reach tens or even hundreds of gigabytes, comparing data on players’ local machines is slow, and this often results in large patch sizes, long installation times, and poor user experience. This article introduces how Electronic Arts (EA) uses AWS to modernize its patching process, reduce update sizes, speed up downloads, and improve player experience.

---

## Initial Challenges: Slow and Inefficient Patching

- Traditional patching only compared files by name, so data moved between files was considered “completely new,” forcing players to re-download many gigabytes of data.  
- Large block sizes used for client-side comparison (e.g., 64 KB) reduced the ability to reuse old data, causing patches to grow in size.  
- Patch computation happened on players’ machines, taking many minutes or longer, especially for large games, negatively impacting user experience.

---

## The Solution: Known Version Patching (KVP) on AWS

In August 2023, EA introduced **Known Version Patching (KVP)** — a server-side patching system running entirely on AWS.

### How KVP Works:
- Each game version is downloaded and unpacked on AWS, and hashes are calculated for every version.  
- The system compares hash metadata between the new version and all previous versions to precisely identify changed data.  
- Incremental patches are generated, containing only the modified data.  
- A coordinator microservice receives events when a new version is available and triggers the patching pipeline and parallel processing jobs.  
- **Amazon Elastic Kubernetes Service (EKS)** is used to run massive parallel jobs.  
- **Amazon Elastic File System (EFS)** stores unpacked builds, enabling reuse and avoiding re-downloading or recalculating for older versions.

---

## Technical Architecture & Patching Workflow

- When a new version is available, a streaming system (e.g., Amazon MSK) sends notifications to trigger the pipeline.  
- The coordinator service creates EKS jobs to download, extract, hash, compare metadata, and generate patches.  
- Patches are uploaded to a CDN and distributed to players through the EA application.  
- EFS caches unpacked builds, so future patches only compare changed parts — saving time and compute resources.  
- EKS uses CPU affinity, autoscaling, and optimized scheduling to maximize cluster throughput.

---

## Benefits and Improvements Achieved

- **Smaller patch sizes:** Patches were reduced by up to **~80%** by including only changed data.  
- **Faster download and installation:** Players can download and install patches up to **3.6× faster**.  
- **Improved player experience:** Less waiting time and reduced frustration during updates.  
- **Cost and resource optimization:** Leveraging AWS infrastructure allows elastic scaling instead of relying on players’ local machines.  
- **Faster development cycles:** Automated patching significantly accelerates release and testing cycles.

---

## Conclusion

By moving patch computation to AWS and applying the KVP system (EKS + EFS), EA achieved a major leap in game update efficiency: smaller, faster, more cost-effective, and more stable updates. Players receive patches more quickly, while EA reduces development time and infrastructure load.

This solution demonstrates the power of AWS in handling massive data workloads at global scale and delivering superior experiences for both developers and gamers.

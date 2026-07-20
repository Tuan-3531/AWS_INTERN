---
title: "Blog 1"
date: 2026-07-08
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Why Epic Games Developed Lore and How AWS Helps Optimize Binary Asset Storage

Hello AWS Study Group VN community! I'd like to share this article with everyone.

If you've ever developed games with thousands of textures, models, animations, or engine binaries, you've probably noticed that traditional version control systems like Git are not well suited for binary files. Every time a binary file of several hundred megabytes is modified, the system often has to store almost the entire file as a new version, even if only a very small portion has changed. Over time, the amount of stored data grows rapidly, leading to increasingly higher storage costs.

To address this challenge, Epic Games developed Lore—an open-source version control system designed specifically for binary assets. AWS has also introduced a reference architecture for deploying Lore on the cloud.

In this article, we'll explore:

* How does Lore differ from traditional version control systems?
* What does the AWS deployment architecture look like?
* Why does this approach help optimize storage costs?

---

## 1. How Does Lore Work Differently?

Instead of storing the entire file after every modification, Lore divides each binary into multiple fragments (chunks) and identifies them using cryptographic hashes. This means:

* Only fragments containing modified data are stored.
* Existing fragments are reused whenever possible.
* A fragment that appears in multiple files or projects only needs to be stored once.

As a result, storage usage grows much more slowly as projects evolve.

---

## 2. Why Does Lore Help Reduce Storage Costs?

AWS highlights three key advantages:

* **Reduced storage usage**: Only modified data is stored instead of the entire file.
* **Nearly free branching**: A new branch simply references existing fragments, without creating additional data unless assets actually change.
* **Cross-project data sharing**: Duplicate fragments are stored only once, optimizing storage resources across the entire studio.

---

## 3. AWS Architecture for Lore

AWS deploys Lore using several familiar services, each responsible for a specific role to ensure performance and scalability:

* **Amazon EC2 (Edge Pods)**: Accepts client connections and caches fragments on NVMe storage for faster access.
* **Amazon ECS (Write Tier)**: Handles deduplication and writes new data.
* **Amazon S3**: Provides durable long-term storage for unique fragments.
* **Amazon DynamoDB**: Manages metadata, locks, and branch information with millisecond-level latency.
* **AWS Cloud Map**: Enables Edge Pods to automatically discover the Write Tier through internal DNS.

This architecture allows the system to scale efficiently while reducing the load on the primary storage layer.

---

## 4. When Should You Use Lore?

Lore is particularly well suited for:

* Game studios with many artists and developers collaborating simultaneously.
* Projects containing large volumes of binary assets.
* Organizations developing multiple game titles at the same time.
* Teams looking to optimize storage costs and synchronize data efficiently on AWS.

---

## Conclusion

Lore introduces a new approach to managing binary assets by breaking data into reusable fragments instead of storing an entire file after every modification.

Combined with **Amazon EC2**, **Amazon ECS**, **Amazon S3**, **Amazon DynamoDB**, and **AWS Cloud Map**, this architecture helps:

1. Significantly reduce storage costs.
2. Accelerate asset synchronization.
3. Support efficient branching.
4. Reuse data across multiple projects.
5. Scale seamlessly as a game studio grows.

For game studios working with large volumes of binary assets, this is a compelling approach to building a modern version control system on AWS.

---

**Original article**: [How Lore rethinks binary asset storage on AWS](https://aws.amazon.com/blogs/gametech/how-lore-rethinks-binary-asset-storage-on-aws/)  
**Tags**: #AWS #AWSForGames #EpicGames #Lore #AmazonS3 #AmazonDynamoDB #AmazonEC2 #AmazonECS #CloudArchitecture #GameDevelopment #VersionControl #BinaryAssets #StorageOptimization

---


---
title: "Blog 3"
date: 2026-07-09
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# How one organization cut AWS costs by 39% in 12 weeks

**Context:** A Platform Engineering team at a SaaS company managing 150 TB of data, with a monthly AWS bill of around $35,000. They were preparing for 10x growth. Without optimization, their AWS bill could balloon to $350,000/month (nearly $4.2 million/year).

Their goal was to combine multiple optimization strategies sequentially in Phases to minimize risk, using the savings from previous Phases to "fund" subsequent ones.

## 5 Cost Challenges

After assessment, they identified 5 main "bottlenecks" causing high costs:

1. Using older generation resources (m5 EC2 instances and gp2 EBS volumes), missing out on the Price-Performance advantages of modern instance types.
2. Amazon S3 communication routed through a NAT Gateway, wasting $2,400/month in data transfer costs.
3. Using over 150 Network Load Balancers (NLB), creating a massive static maintenance cost.
4. 100% of data sitting in S3 Standard, with no storage tiering for infrequently accessed data.
5. Right-sizing based on Peak levels instead of using flexible Auto Scaling, wasting 30-50% of compute capacity.

## Allocation Method: Effort vs. Impact

Instead of doing everything at once, the team used the AWS Cost Optimization Hub to score items based on 3 criteria: Effort, Impact, and Risk. Low-risk tasks that didn't require application code changes were prioritized.

## Execution Process via 3-Phase Strategy

### Phase 1: Foundation Optimization (Weeks 1 - 4)

Focused on Storage, Networking, and Load Balancing. This phase provided the fastest benefits (Quick Wins).
* **Storage (EBS & S3):** Based on AWS Compute Optimizer recommendations, they upgraded (online migration) over 200 volumes from gp2 to gp3 – causing zero downtime. Activated S3 Intelligent-Tiering, automatically moving objects > 128KB to cheaper tiers, saving $1,200/month instantly.
* **Networking:** Using AWS Cost and Usage Reports (CUR) and VPC Flow Logs, the team found 60% of traffic through the NAT Gateway was to S3. They immediately configured a VPC Gateway Endpoint for S3, routing background traffic within the AWS internal network, cutting NAT Gateway costs by $2,400/month.
* **Load Balancer Consolidation:** The old architecture using Kubernetes (EKS) provisioned one NLB per Service by default (over 150 NLBs running). The team consolidated these 150 NLBs into 5 Application Load Balancers (ALBs) divided by Domain groups (API, Web, Internal, Async, Admin). Thanks to the AWS Load Balancer Controller and Route 53 Weighted Routing, they shifted traffic from the old NLBs to the new ALBs (10% -> 50% -> 100%) without dropping a single request. Cleaning up the 150 redundant NLBs saved an additional $3,400/month.

**End of Phase 1:** Saved ~$8,400/month (24% reduction in total bill).

### Phase 2: Compute Right-Sizing (Weeks 4-8)

AWS Compute Optimizer showed that many instances running General-purpose types (m5.4xlarge) were memory-bound (only using 45% CPU but consuming 85% RAM), leading to wasted CPU capacity.

**Action:** Switched to Memory-optimized types using AMD processors (r6a.2xlarge). This maintained the 64 GiB RAM but halved the vCPU count, lowering the cost. By modifying the Launch Template and EC2 Auto Scaling, they performed A/B testing and ensured safe rollbacks if performance wasn't met.

### Phase 3: Advanced Optimization and Next-Gen Architecture (Weeks 8 - 12)

* **Deploying Karpenter:** Replaced the traditional Cluster Autoscaler with the open-source Karpenter for more automatic and flexible node provisioning in EKS.
* **Transitioning to ARM Architecture (AWS Graviton):** The team built container images supporting Multi-architecture (both x86 and arm64). Then, they used Karpenter Disruption Controller to automatically drain old x86 nodes (r6i.2xlarge) and replace them with the Graviton2 series (r6g.2xlarge). Moving to ARM processors saved an additional 20% in compute costs for supported workloads.

## Results & Governance

1. **Results Achieved:** After 12 weeks, the organization reduced its AWS costs by a total of 39% (saving $13,700/month). If they had kept the old architecture during their 10x growth, these efforts equate to saving the company approximately $1.64 million/year.
2. **Sustaining Governance:** The core principle of FinOps is "don't let costs balloon again." The team:
   * Integrated cost review processes into regular Sprint Planning sessions.
   * Established Service Control Policies (SCPs) via AWS Organizations to:
     * Deny the creation of new legacy gp2 volumes (forcing the use of gp3).
     * Only allow resource provisioning from approved Instance Families and within specific AWS Regions.

> **Reference:** [How one organization cut AWS costs by 39% in 12 weeks](https://aws.amazon.com/blogs/aws-cloud-financial-management/how-one-organization-cut-aws-costs-by-39-in-12-weeks/?content_source=fb&fb_content_id=Q9-wBQFW206aJU5vuo2CKD0obi06M-KIiAdM-zCAU_SURad5dGSS7XLwpBlxbO1QSg&channel_type=fb)

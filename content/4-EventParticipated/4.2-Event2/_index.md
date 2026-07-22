---
title: "Event 2"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 4.2. </b> "
---

# Event Review: June 13th Tech Event

### Purpose of the Event

- Career orientation and sharing personal growth journeys in the Information Technology industry.
- Analyze a real-world system architecture (Case study: URL Shortener Application) on Cloud computing platforms.
- Provide in-depth insights into the Data Analytics industry and the career path across levels.
- Understand the recruitment workflow, culture of Multinational Corporations (MNCs), and work philosophy.
- Present the realistic view of a DevOps Engineer compared to theoretical concepts.

### Speakers

- **Mr. Danh Hoang Hieu Nghi** - Topic: Career journey and identifying users' actual pain points.
- **Mr. Trung Kien (Leader) & Mr. Nguyen Minh Tho** - Topic: System Architecture Analysis of a URL Shortener project.
- **Mr. Dat Pham** - Topic: Data Analytics and Career mindset roadmap.
- **Mr. Nguyen Cuong** - Topic: Corporate culture (MNC) and the "Right Job" philosophy.
- **Mr. Trong H. Truong (DevOps at Endava)** - Topic: What does a DevOps Engineer really do?

### Key Highlights

#### Career Journey & Problem Solving (Mr. Hieu Nghi)

- **Orientation**: Sharing about personal development and career growth in the tech field.
- **Mindset**: Emphasized the importance of deep-diving into the core problem to identify the user's actual difficulty. Only then can we propose an accurate, value-driven technical solution.

#### URL Shortener Architecture (Mr. Trung Kien & Mr. Minh Tho)

- **Solving Bottlenecks**: Defined system issues when handling high traffic loads and how to use cloud services to solve them.
- **Core 3-tier System Architecture**:
    - **Frontend & Edge Layer**: Seamless combination of Amazon CloudFront, AWS WAF, and AWS Amplify.
    - **Processing Layer (Compute & Cache)**: Used Amazon ECS to run shortener logic. Requests flow from ECS to ElastiCache (Redis).
    - **Database Layer**: Queries directed to Amazon DynamoDB.
- **Cache-aside pattern**: When a link-retrieval request arrives, the system checks ElastiCache first (returning immediately on a cache hit). On a cache miss, the system queries DynamoDB, retrieves the link, and writes it back to ElastiCache.
- **4 Architectural Benefits**:
    1. Separation of concerns (Clear separation of logic).
    2. Defense at the edge (Security implemented via WAF & CloudFront).
    3. Pre-computation (Data calculated in advance).
    4. Cache-aside pattern (Optimized cache lookup flow).

#### Data Analytics & MNC Culture (Mr. Dat Pham & Mr. Nguyen Cuong)

##### Part 1: Data Analytics (Mr. Dat Pham)

- **Role**: Preparing weekly reports, finding root causes, communicating with departments. Cost analysis (detail, risk) and business metrics (order conversion rate).
- **Core Skills**: Critical thinking, communication skills, data storytelling, and a growth mindset.
- **Mindset**: Explain things from the perspective of someone who knows nothing about technology. Truly understand what the numbers represent in the real world.
- **5 Levels of Career Progression**:
    - Follower: Intern, needs task-by-task guidance.
    - Learner: Understands work but still needs supervision.
    - Problem Solver: Can handle assigned tasks independently.
    - System Thinker: Looks at the problem globally (e.g., how a new regulation impacts the company).
    - Super Star: Builds system vision, maps out long-term strategy, and positions the business.

##### Part 2: MNC Culture & "Right Job" Philosophy (Mr. Nguyen Cuong)

- **MNC Recruitment**: 4 rounds (Resume screening/Pre-screening -> Competency test -> Technical interview -> Culture fit evaluation).
- **Corporate Culture**: The sum of "ways of thinking, ways of living, ways of working" towards the tasks of all members.
- **Integration Context**: Analyzed milestones in Vietnam (1975-1986 isolation, 1986-1995 Doi Moi, 1997 Internet & joining WTO). Emphasized that Vietnam needs to move toward international standards, especially by avoiding any loss of data security credibility.
- **"Right Job" Philosophy (based on the book by Dr. Gian Tu Trung)**:
    - Being a human: Kindness (Social domain).
    - Doing a job: Solving real-world problems with a service spirit (Economic domain).
    - Being a citizen: Responsibility to the nation, carrying the country's destiny (Political domain).

#### Reality of DevOps (Mr. Trong H. Truong)

- **Why choose DevOps**: Cool, good income, high employment potential. Compared to the rise of AI, DevOps still holds a vital position.
- **Reality vs. Theory**: Learning theory is creating code, CI/CD, Docker, fixing bugs at midnight. In reality, depending on company scale (Startup vs. Enterprise), workload varies. In startups, members often have to work cross-field and wear multiple hats.
- **Toolbox (Tools)**: Must learn broadly from Linux, Network, Git, coding languages, CI/CD to Containers. However, tools change constantly.
- **Vital DevOps Mindset**:
    - Double check copy-pasted code; learn from real-world failures.
    - Communication is key (know how to talk to other teams).
    - DevOps is not a helper department that runs around fixing everything! They need space to resolve incidents that happen suddenly.
    - System thinking (System Thinker) instead of focusing on tasks. Build clean, readable systems.
    - Use AI to boost productivity, do not let your brain get lazy.
- Shared his own journey of building a product to accumulate experience.

### Lessons Learned

#### Design & Technical Mindset

- Mastered the Cache-aside architecture and the power of Separation of Concerns.
- Understood the importance of "Defense at the edge" by blocking malicious traffic at the CDN and WAF layers instead of letting them hit the backend compute.
- Understood that in Data, numbers are lifeless without a "storyteller". Always put yourself in the end-user's shoes to understand what numbers represent.

#### Soft Skills & Career Path

- Career growth is not just about technical skills (coding/tooling) but depends on shifting from a Problem Solver mindset to a System Thinker mindset.
- Communication is a survival skill in both Data Analytics and DevOps to bridge departments, resolve conflicts, and unify workflows.
- Core principle: Learn to use AI to optimize work processing efficiency without relying on it so much that you lose core thinking.

#### Application to Work

- **Applying "Defense at the Edge"**: Configured AWS WAF combined with Amazon CloudFront and Amplify to protect the static frontend app, blocking DDoS and Prompt Injection before requests reach API Gateway.
- **Backend Optimization Mindset**: Leveraging DynamoDB for storage and auto-scaling Lambda for compute implements the Separation of Concerns principle, aligning with the speakers' analysis.
- **Developing as a System Thinker**: When building architecture, instead of completing isolated tasks, I look at the overall data flow (Route 53 -> Frontend -> API Gateway -> Lambda -> DynamoDB/Bedrock) to manage errors holistically, raising myself from Learner to Problem Solver/System Thinker.
- **Mindset for the Work Environment**: Better understand the MNC culture and the "Right Job" philosophy to enhance professionalism during the internship.

### Event Experience

- The event provided practical, real-world perspectives rarely discussed in university classrooms. The most impressive part was the seamless connection between theory and AWS architecture in the URL Shortener case study, illustrating the power of cloud computing for high-traffic systems.
- Furthermore, the sessions on Data Analytics and DevOps broke my naive perceptions. I realized that whether analyzing data or operating infrastructure, the defining factors for success are **Communication** and **System Thinking**. The concept of working with a "service spirit" and the "Right Job" philosophy serve as valuable guidelines, helping me reshape my work attitude and build high responsibility for any line of code or design diagram I create.

#### Some event photos

![Event Photo](/images/4-EventParticipated/Event2/1783679263617_118571171431119328_g2710655335341090523_88121a123a90aa42bd6a7d6b606595ce.jpg)
![Event Photo](/images/4-EventParticipated/Event2/1783679263622_118571171431119328_g2710655335341090523_acbde0923b02fe0cc5322e38e358e398.jpg)
![Event Photo](/images/4-EventParticipated/Event2/1783679263626_118571171431119328_g2710655335341090523_3163d5fa6c753f52282132d7523da0f4.jpg)
![Event Photo](/images/4-EventParticipated/Event2/1783679263630_118571171431119328_g2710655335341090523_f29790f1ac468a56789b01ab3f9c1795.jpg)


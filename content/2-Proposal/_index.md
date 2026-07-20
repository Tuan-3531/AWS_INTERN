---
title: "Proposal"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# AI Riddle Generator 
## Automated Cognitive Riddle Generator for Kids Using Unified AWS Serverless Solution

### 1. Executive Summary  
The AI Riddle Generator is an Artificial Intelligence (AI) integrated educational application platform designed to automatically transform keywords or answers entered by users into riddles, hint paragraphs, or cryptograms (such as acrostic verses). The system targets primary and secondary school students to stimulate logical thinking, creativity, and broaden foundational knowledge. The project entirely utilizes the AWS Serverless architecture to optimize operational costs, ensure real-time response capability, and manage centralized user authentication via Amazon Cognito, serving parents and teachers directly.  

### 2. Problem Statement  
*Current Problem*  
Parents and teachers often spend a lot of time, effort, and easily run out of ideas when wanting to design brain games, treasure hunt activities, or ice-breaking exercises for students. Current riddle-making tools are scattered, lack customization based on lessons or native cultural/historical themes, and do not exploit the power of Generative AI to intelligently automate the process.

*The Solution*  
Build the AI Riddle Generator system utilizing large language models (LLMs) via Amazon Bedrock to generate riddles automatically. The system uses AWS Lambda as the core logic processor (comprising functions for riddle generation and featured list management), and stores riddle data and user interactions in the Amazon DynamoDB database. The entire system communicates via Amazon API Gateway (HTTP API) and distributes the Frontend (React/Vue) quickly via AWS Amplify. User access and identity are strictly secured through Amazon Cognito. 

*Benefits and Return on Investment (ROI)*  
*Benefits*: Frees up lesson/game preparation time for teachers and parents; increases lively interaction in education. At the same time, this is a hands-on Cloud Serverless architecture practice platform for the student development team.
*Costs & ROI*: Thanks to the Serverless model (pay-per-use) combined with AWS Free Tier limits for AWS Lambda, API Gateway, and DynamoDB, estimated monthly operational costs are extremely low (nearly $0 USD at small scale, except for Amazon Bedrock AI API token fees). The break-even period is immediate due to saving dozens of hours of manual content preparation each month.

### 3. Solution Architecture  
The system employs a comprehensive AWS Serverless architecture to eliminate server management and automatically scale based on online user traffic. 

![Project Service Structure](/images/2-Proposal/Cau-Truc-Service-Du-An.png)

*AWS Services Used*  
- *Amazon Bedrock*: Invokes AI Foundation Models (via `@aws-sdk/client-bedrock-runtime`) to automatically generate riddles from keywords.
- *AWS Lambda*: Contains 2 main handler functions: `generateRiddle` (invokes AI Bedrock and saves results) and `getFeaturedRiddles` (retrieves the featured riddles list).
- *Amazon DynamoDB*: NoSQL database storing riddle information, answers, and upvote/interaction data.
- *Amazon API Gateway*: Configures HTTP APIs to route endpoints (`POST /riddles/generate`, `GET /riddles/featured`, `POST /riddles/upvote`) and enables CORS to allow Frontend connection.
- *AWS Amplify*: Connects directly to the GitHub repository to automatically build and deploy the Frontend (React/Vue) interface.
- *Amazon Cognito*: Manages registration, login, and authorization for user roles (Parents/Teachers).

*Component Design*  
- *User Interface (Frontend)*: React/Vue application deployed on AWS Amplify, using the `axios` or `fetch` library to send requests to the API.
- *API & Processing Layer (Backend)*: API Gateway intercepts requests $\rightarrow$ Triggers the corresponding Lambda $\rightarrow$ Lambda processes business logic and interacts with AI/Database.
- *Artificial Intelligence (AI Layer)*: Amazon Bedrock receives prompts from Lambda, processes semantics, and returns riddle content in text/poetry format.
- *Storage Layer (Database Layer)*: DynamoDB stores created riddles for reuse and serves the featured riddles display feature. 

### 4. Technical Implementation  
*Implementation Phases*  

1. *Prompt Engineering Design & Research*: Research how to optimize prompt structures for models on Amazon Bedrock to ensure generated riddles are suitable for primary and secondary school students. Draw the detailed system architecture diagram.  
2. *Backend & Database Development*: Set up tables on DynamoDB. Write AWS Lambda functions using Node.js/Python, integrating Bedrock SDK and DynamoDB SDK. Configure HTTP APIs on API Gateway and verify internal connectivity.
3. *Frontend Development & Integration*: Development team builds the React/Vue interface on local machines, integrating endpoints called from API Gateway. Push source code to GitHub.
4. *Deployment & Testing*: Connect AWS Amplify with GitHub to set up CI/CD for automatic Frontend deployment. Integrate Amazon Cognito for security. Perform comprehensive End-to-End testing and launch into production.

*Technical Requirements*  
- *Client Side*: Responsive web interface (running smoothly on teachers' computers and parents' smartphones). Clean code, proper state management to handle loading states while AI is generating riddles.
- *Cloud Side*: Proficient in configuring secure IAM Roles/Policies allowing Lambda to invoke Bedrock and write data to DynamoDB. Configure CORS on API Gateway to avoid domain blocking errors. Understand AWS SDK usage to optimize Lambda execution times.

### 5. Timeline & Milestones  
- *Pre-Internship (Month 0)*: Survey actual needs from teachers/parents and draft core features (riddle creation, age filtering, acrostic verse system).  
- *During Internship (Month 1 - Month 3)*:  
    - Month 1: Finalize system design, research Amazon Bedrock, and set up development environments.  
    - Month 2: Complete programming of Lambda functions, configure API Gateway, and complete the basic Frontend interface locally.  
    - Month 3: Deploy to AWS Amplify, configure CI/CD via GitHub, test security with Cognito, and accept the project.  
- *Post-Deployment*: Monitor costs, optimize prompts based on user feedback to improve riddle quality, and expand into new riddle formats (complex visual/cryptic riddles).  

### 6. Budget Estimation  
Estimated costs can be viewed on [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=621f38b12a1ef026842ba2ddfe46ff936ed4ab01).  
Or you can download the [Budget Estimation File](../attachments/budget_estimation.pdf).  

*Infrastructure Costs*  
- *AWS Lambda*: $0.00 (Within the limit of 1 million free requests per month).  
- *Amazon DynamoDB*: $0.00 (25GB free storage limit is sufficient for millions of text riddles).  
- *Amazon API Gateway (HTTP API)*: ~$0.01 (Very cheap, billed per actual million requests).  
- *AWS Amplify*: Free Tier for the first year (or about $0.35 - $1.00/month for small bandwidth and storage).  
- *Amazon Bedrock*: Cost based on actual token usage (e.g., ~$0.0015 per 1,000 output tokens of Claude/Titan models, estimating a few dollars for thousands of riddles).  
- *Hardware/Devices*: $0 (Use existing personal computers of members for coding, no need to purchase physical servers).  

Total Estimated Cost: ~$2.00 - $5.00/month (primarily Amazon Bedrock API usage cost depending on riddle generation frequency).  

### 7. Risk Assessment  
*Risk Matrix*  
- AI generates inappropriate content (Hallucination/Toxic content): High impact, low probability.  
- CORS error or API connection blockage: Medium impact, medium probability (common during initial API Gateway configuration).  
- High latency during AI calls: Medium impact, medium probability (as AI requires time to process semantics and generate verses/riddles).  

*Mitigation Strategies*  
- AI Content: Set a strict System Prompt on Bedrock, forcing the AI to focus only on healthy educational topics for students.  
- CORS Errors: Correctly set `Access-Control-Allow-Origin` configurations on API Gateway right from the route configuration phase.  
- Latency: Add lively animated loading effects on Frontend so users do not feel they are waiting too long.  

*Contingency Plans*  
- If Amazon Bedrock encounters service disruption, the system will automatically fall back to fetching existing riddles (stored with the highest upvotes) in DynamoDB to temporarily display to users.

### 8. Expected Outcomes  
*Technical Improvements*: Successfully build a complete Fullstack Web application utilizing GenAI, automating 100% of the real-time riddle generation process instead of manual drafting. The system runs stably, securely, and responds quickly in under 5 seconds.
*Long-term Value*: Provides a useful, visual digital education tool for the teacher and parent community. Concurrently builds a rich database of educational riddles, opening development avenues for smart educational games in the future.
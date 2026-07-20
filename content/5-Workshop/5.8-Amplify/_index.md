---
title : "Deploy Frontend to AWS Amplify"
date : 2024-01-01 
weight : 8 
chapter : false
pre : " <b> 5.8. </b> "
---


#### Why AWS Amplify?
AWS Amplify Hosting is a fully managed service that provides secure, reliable static web hosting. It is selected for our AI Riddle Generator presentation tier because it integrates directly with Git providers (like GitHub) to offer automated CI/CD out-of-the-box. Every code change pushed to the connected branch automatically triggers a rebuild and redeployment. Sticking with default configurations is optimal as Amplify automatically detects standard frontend framework configurations (React, Vue, etc.) and optimizes asset caching without manual management.

---

### Step-by-Step Deployment

1. Navigate to the **AWS Amplify** console. If you do not have any apps, click **Deploy an app**. Otherwise, click **Create new app**.

   ![deploy-app](/images/5-Workshop/5.8-Amplify/deloy-app.png)

2. Choose **GitHub** as the source provider to connect your code repository and click **Next**.

   ![select-github](/images/5-Workshop/5.8-Amplify/Chọn%20GitHub.png)

3. Authenticate with your GitHub account when prompted to authorize AWS Amplify to access your repositories.

   ![auth-github](/images/5-Workshop/5.8-Amplify/Xác%20thực%20GitHub.png)

4. Select your frontend repository and choose the specific branch containing your interface code (e.g. `main` or custom frontend branch). Click **Next**.

   ![select-branch](/images/5-Workshop/5.8-Amplify/nhánh%20giao%20diện.png)

5. Under Build settings, stick to the **default settings**. Amplify automatically auto-detects the build command (e.g., `npm run build`) and output directory. Click **Next** to review, and then click **Save and deploy**.

   ![save-deploy](/images/5-Workshop/5.8-Amplify/save%20and%20deploy%20app.png)

6. Wait a few minutes for the build phases (Provision, Build, Deploy) to complete.

   ![deployed-site](/images/5-Workshop/5.8-Amplify/deploy%20ra%20trang%20web.png)

Once finished, AWS Amplify will generate a default web URL (e.g., `https://main.d1w4ziet0548q.amplifyapp.com/`). Note this URL down, as it will be used as the origin in the next CloudFront setup.

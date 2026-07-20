---
title : "Configure Amazon Cognito as Authorizer"
date : 2024-01-01 
weight : 11 
chapter : false
pre : " <b> 5.11. </b> "
---


#### Securing API Endpoints with Amazon Cognito
To protect the backend API resources of the **AI Riddle Generator** from unauthorized access, we will integrate **Amazon Cognito** identity service. By defining a Cognito User Pool as an Authorizer on API Gateway, only authenticated users who submit a valid JWT Token can successfully trigger the backend business processes.

---

### Step-by-Step Configuration

#### Part A: Initialize Cognito User Pool

1. Access the **Amazon Cognito** console on AWS, click **User pools**, and select **Create user pool**.

   ![create-userpool](/images/5-Workshop/5.11-Cognito/create-cognito.png)

2. Set up the App client:
   - For App type, choose **Single-Page Application (SPA)**.
   - Enter your client app name (**App client name**).
   - Select **Email** as the sign-in option attribute.

   ![select-spa](/images/5-Workshop/5.11-Cognito/chon-spa-and-name.png)

3. Configure sign-up and attributes settings:
   - Check the **Enable self-registration** checkbox to allow users to sign up themselves.
   - For required attributes, check the **name** attribute checkbox.
   - Proceed to confirm and create the User Pool.

   ![configure-options](/images/5-Workshop/5.11-Cognito/configure-option.png)

4. Cognito User Pool created successfully. Record the **User Pool ID** and **Client ID** to integrate into your React/Vue web UI client configuration.

   ![cognito-created](/images/5-Workshop/5.11-Cognito/thanh-cong-cognito.png)

---

#### Part B: Setup Authorizer on API Gateway

1. Return to the **API Gateway** console and open the `AI-Riddle-API` you created in the previous section. Click **Authorizers** in the left menu, then click **Create authorizer**.

   ![create-authorizer](/images/5-Workshop/5.11-Cognito/Create-authorizes.png)

2. Configure Authorizer parameters:
   - **Authorizer name**: Enter a name (e.g. `Cognito-Auth`).
   - **Type**: Select **Cognito**.
   - **Cognito user pool**: Choose the region and select the user pool you initialized.
   - **Token source**: Enter `Authorization` (this represents the client header holding the ID token).
   - Click **Create authorizer**.

   ![authorizer-details](/images/5-Workshop/5.11-Cognito/chi-tiet-aut.png)

3. Cognito authorizer successfully created on API Gateway.

   ![authorizer-created](/images/5-Workshop/5.11-Cognito/thanh-cong-auth-api.png)

---

#### Part C: Enable Cognito Auth on API Methods

1. Select **Resources** in the left menu of API Gateway, click the method to secure (e.g. `POST` under `/Create-Riddle`). In the method dashboard, select the **Method request** tab and click **Edit**.

   ![select-method-auth](/images/5-Workshop/5.11-Cognito/chon-auth-cho-api.png)

2. Change **Authorization** settings from `NONE` to your Cognito authorizer (e.g. `Cognito-Auth`), and click **Save**.

   ![save-method-auth](/images/5-Workshop/5.11-Cognito/cai-cognito-vao-api.png)

3. Cognito Auth is now assigned to the selected API Gateway method.

   ![auth-linked](/images/5-Workshop/5.11-Cognito/thanh-cong-gan-auth-vao-lenh-api.png)

4. **Deploy API** again to stage `dev` to apply the authorization settings.

Now all requests sent to API Gateway must contain the `Authorization` Header holding a valid JWT ID Token from the Cognito User Pool!

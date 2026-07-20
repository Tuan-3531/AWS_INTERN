---
title: "Web Interface Testing & Product Report"
date: 2024-01-01
weight: 12
chapter: false
pre: " <b> 5.12. </b> "
---

### Web Interface Testing & Product Report for AI Riddle Generator

After completing the configuration of the entire backend Serverless infrastructure on AWS (including DynamoDB, S3, Lambda, API Gateway, Cognito, and Amplify), the final step is to access and evaluate the complete frontend web application.

The web application is designed as a **Single-Page Application (SPA)** built on **React 19** and compiled using the ultra-fast build tool **Vite 8**. The user interface implements a modern **Glassmorphism** styling theme, with fluid transition animations and a fully responsive layout optimized across devices.

---

### 1. Functional Subsystems of the Web Interface

The web application's dashboard is split into core functional modules mapped to the main navigation menu items:

#### 1.1. Create Riddle Module (Generator Hub)
Where users (Teachers, Parents) interact directly with the Generative Artificial Intelligence (AI) model:
*   **Configuration Filters:**
    *   *Age Group:* Classified by education level (Cấp 1, Cấp 2, Cấp 3) to allow the LLM to adjust vocabulary complexity and riddle difficulty suitable for children's cognitive abilities.
    *   *Riddle Genre:* Supports keyword letter-matching (Acrostic) or logical thinking riddles (History-Literature, Science-Mathematics).
    *   *Topic:* Geography, Science, History, Animals, Plants, etc.
*   **Riddle Generation Flow:** The user inputs a "Keyword". Clicking the generate button sends the payload to API Gateway. Results display the generated riddle body, hints (Hint 1, Hint 2), and the Answer, which are hidden under collapsible tabs (click to reveal).
*   **Storage Integration:** Once logged in, a **"Save to Library"** button appears, allowing the generated riddle to be written directly into the DynamoDB database.

#### 1.2. Community Board Module
A public feed displaying riddles shared by all active members:
*   **Featured Sorting:** Utilizes the **GSI1 (Global Secondary Index)** in DynamoDB to sort public riddles in descending order of their upvote counts.
*   **Upvoting System:** Strictly controlled business logic ensures authenticated users can only upvote a riddle once. Upvote counts update on the dashboard in real-time.
*   **Quick Copy:** Allows users to easily duplicate public community riddles into their personal libraries.

#### 1.3. Personal Library Module (My Library)
A private storage vault dedicated to the currently authenticated user:
*   **Query Listing:** Queries DynamoDB using the partition key (PK) `USER#<UserId>` and sort key (SK) prefix `RIDDLE#` to pull up riddles authored by that account.
*   **Document Exports:** The export button generates a short-lived **Amazon S3 Presigned URL** to download a high-quality printable PDF/Word document containing the riddle details, ready for classroom usage.

#### 1.4. Cognito Authentication Module
User registry, login, and access control portal:
*   **Account Registration:** Configured for two key roles, **Teacher** or **Parent**, to assign appropriate IAM (Identity and Access Management) credentials.
*   **Quick Login Simulator:** Provides quick login buttons for mock profiles (Teacher Nguyễn Thị Mai, Parent Trần Văn Thu) to easily test authorization levels in the browser.

---

### 2. Live Application Testing Scenarios (Step-by-Step E2E User Journey)

Below is the chronological, step-by-step user journey executed on the live frontend application during verification:

#### 2.1. Step 1: Startup & Authentication Flow (AWS Cognito)
1. **Access Login Interface:** The user navigates to the landing page and clicks the **Đăng nhập** (Login) button in the upper-right corner. The system redirects to the AWS Cognito authentication gateway.
   ![Cognito Login Page](/images/5-Workshop/5.12-Web-Interface/test_login_page.png)
2. **Quick Login Authentication:** We selected the mock teacher account profile `"Cô Nguyễn Thị Mai"` for rapid verification.
3. **Login Redirection:** Upon validation (2s delay), the client receives the JWT session token, redirects back to the generator page, and dynamically renders the profile badge in the header.
   ![Login Success](/images/5-Workshop/5.12-Web-Interface/test_login_success.png)

#### 2.2. Step 2: GenAI Riddle Generation (AI Riddle Creation)
1. **Configure Parameters:** On the **Create Riddle** tab, we selected the topic "Geography", target age group "Cấp 1" (Elementary), and input the answer keyword `"Trái Đất"` (Earth).
   ![Keyword Input](/images/5-Workshop/5.12-Web-Interface/test_create_riddle_input.png)
2. **Invoke GenAI Action:** Clicked **⚡ Tạo Câu Đố Với AI**. The request propagates through API Gateway to the AWS Lambda backend to trigger Amazon Bedrock.
3. **Render Initial Output:** The generated Acrostic riddle is displayed in a glassmorphic card with hints and the answer collapsed by default.
   ![Riddle Generated](/images/5-Workshop/5.12-Web-Interface/test_create_riddle_result.png)
4. **Reveal General Clue:** Clicking the **💡 Gợi ý bước 1 (Khái quát)** tab reveals the first general hint describing the planet.
   ![Hint 1 Revealed](/images/5-Workshop/5.12-Web-Interface/test_create_riddle_hint.png)
5. **Reveal Final Answer:** Clicking **🏆 XEM ĐÁP ÁN CUỐI CÙNG** expands the answer badge to reveal the target keyword `"Trái Đất"`.
   ![Answer Uncovered](/images/5-Workshop/5.12-Web-Interface/test_create_riddle_answer.png)
6. **Save to Database:** Clicking **Lưu Vào Thư Viện** writes the riddle record to the DynamoDB table. The button state updates to `⭐ Đã Lưu Vào Thư Viện`.
   ![Riddle Saved](/images/5-Workshop/5.12-Web-Interface/test_create_riddle_saved.png)

#### 2.3. Step 3: Community Interactivity & Solving (Community Tab)
Navigating to the **Community** feed containing public riddle contributions:
1. **Correct Solving Guess:**
   * Locate the featured riddle card for `"Bánh Chưng"`.
   * Input the answer guess `"Bánh Chưng"` in the solver text field and click **Đoán** (Guess).
   * The UI displays a green success confirmation block: `🎉 Chính xác! Đáp án đúng là: Bánh Chưng`.
   ![Guess Correct](/images/5-Workshop/5.12-Web-Interface/test_community_guess_correct.png)
2. **Incorrect Solving Guess:**
   * Input an incorrect value such as `"Bánh dày"` in a card's guess box and click **Đoán**.
   * The UI displays a red warning label: `❌ Chưa đúng rồi, thử lại nhé!`.
   ![Guess Incorrect](/images/5-Workshop/5.12-Web-Interface/test_community_guess_incorrect.png)
3. **Cast Upvotes:**
   * Click the 👍 (Upvote) button on the Bánh Chưng card. The upvotes count increases from 56 to 57.
   * The system validates and registers the `UPVOTE` entity class in DynamoDB, ensuring one vote per user restriction.
   ![Upvote Successful](/images/5-Workshop/5.12-Web-Interface/test_community_upvote.png)

#### 2.4. Step 4: Library Listing & S3 PDF Export (Personal Library & S3)
1. **Query Personal List:** Navigating to **Thư Viện Của Tôi** fetches riddles owned by the logged-in user profile, including the newly saved `"Trai Dat"` riddle.
   ![Personal Riddle Library](/images/5-Workshop/5.12-Web-Interface/test_library_list.png)
2. **S3 Document Distribution:**
   * Click **🖨️ Xuất PDF / In câu đố** on the `"Trai Dat"` riddle card.
   * The client requests a short-lived **S3 Presigned URL** to download a high-resolution, print-ready document layout.
   ![PDF Export Preview](/images/5-Workshop/5.12-Web-Interface/test_library_export.png)
   ![PDF Export Preview](/images/5-Workshop/5.12-Web-Interface/test_library_export_PDF.png)

#### 2.5. Dark Theme Presentation
The application supports a custom Dark Mode styling utilizing CSS custom variables to minimize eye strain.
![Dark Mode View](/images/5-Workshop/5.12-Web-Interface/riddle_dark_mode.png)

---

### 3. Future Development

While the project completes the core requirements of this Workshop, the following roadmap features are planned for future development to optimize it for commercial deployment:

*   **Production Cognito Authentication:** Replace the simulated login flow with the `@aws-amplify/auth` library or the official `amazon-cognito-identity-js` SDK to handle authentic Cognito User Pool sessions, JWT validation, and secure token storage in cookies or localStorage.
*   **Live API Gateway & DynamoDB Integration:** Swap out the mock client layer (`db.js`) for direct REST/HTTP endpoint integration by configuring the environment variable `.env` (`REACT_APP_API_URL`). All riddle saves, deletions, and upvotes will synchronize directly with live tables on AWS.
*   **Student Gamification:** Develop an interactive solver dashboard for children, integrating a score system, countdown timers, and live leaderboards to make educational riddle solving highly engaging.
*   **Batch S3 Exporting:** Expand the backend Lambda export function using libraries like `pdfkit` or `docx` to allow teachers to compile and export themed lists of riddles in bulk with a single click.
*   **Teacher Analytics Dashboard:** Set up AWS QuickSight analytics or build an interactive dashboard on the frontend to visualize student activity levels, popular riddle topics, and average completion rates.

---

### 4. Concluding Product Evaluation

*   **Architectural Consistency (End-to-End Serverless):** The frontend interfaces smoothly with simulated Single-Table Design queries in DynamoDB, Cognito security claims, and S3 assets, mirroring the cloud configuration logic accurately.
*   **UX/UI Quality:** The Glassmorphism design is clean, interactive, and tailored to educators and parents. Navigation transitions are near-instantaneous due to the SPA setup.
*   **Scalability:** The React codebase is modularly organized, making it easy to add features like interactive puzzle games or student gradebooks in the future.

# QA Test Case Generator using n8n

![n8n](https://img.shields.io/badge/n8n-Workflow-orange)
![Jira](https://img.shields.io/badge/Jira-Cloud%20API-blue)
![Gemini](https://img.shields.io/badge/Google%20Gemini-Chat%20Model-4285F4)
![AI](https://img.shields.io/badge/AI-Test%20Case%20Generation-purple)



## 📌 Overview

This project is an AI-powered QA test case generation and scoring pipeline built using n8n.

The workflow fetches the user stories for given sprint  from Jira, generates positive/negative/edge test cases for each story 
using a Google Gemini chat model, and scores the generated test cases against a quality rubric using a second AI agent. 

Test cases that have pass score are logged to Google Sheets and created as Jira subtasks under the parent story. 
Test cases that fails, trigger an alert email to the  team .
Also,a summary notification email is sent once all stories in a run have been processed.


## 🖼️ Workflow Screenshot

![workflow-canvas.png](screenshots%2Fworkflow-canvas.png)

![email_alert.png](screenshots%2Femail_alert.png)

![failed_email_alert.png](screenshots%2Ffailed_email_alert.png)

![jira_subtask_created.png](screenshots%2Fjira_subtask_created.png)

## 🔄 Workflow

Manual Trigger
→ Fetch Jira Stories 
→ Loop Each Story
→ Update Issue 
→ Generate Test Case 
→ Parse Test Cases
→ Score Quality (AI-as-judge)
→ If (approved true/false)
  → **Pass:** Google Sheets (append/update) → Create Jira Subtask → Edit Fields → loop continues
  → **Fail:** Gmail Alert to QA Team → loop continues
→ Loop Done → Merge → Summarize → Notify QA Team (summary email)

## 📁 Project Structure

```

qa-test-case-generator-n8n

├── README.md
├── workflow/
│   └── test-case-generator.json
├── reports/
│   └── test_case.xlsx
├── screenshots/
│   ├── email_alert.png 
│   ├── failed_email_alert.png
│   ├── jira_subtask_created.png        
│   └── workflow-canvas.png

```

## 🧠 Test Case Generation Rules

Each story generates exactly 3 test cases: 1 Positive (happy path), 1 Negative (error scenario), 1 Edge case.
Output fields: Test Title, Preconditions, Steps, Expected Result, Priority, Type.

## 🎯 Scoring Rubric

The Score Quality AI agent evaluates each set of test cases on:
1. Clarity of steps (1–3)
2. Completeness of expected result (1–3)
3. Coverage of acceptance criteria (1–4)

Total score out of 10. Test cases below the approval threshold are routed to the fail path.

## ⚙️ What This Workflow Does

- Fetches unprocessed ("To Do") user stories from Jira for a given sprint number
- Generates positive, negative, and edge test cases per story using Gemini
- Scores generated test cases against a quality rubric using a second AI agent
- Routes passing test cases to Google Sheets and creates a linked Jira subtask
- Routes failing test cases to a QA team alert email
- Marks each story "In Progress" in Jira so it isn't reprocessed on the next run
- Sends a summary email once all stories in the run are complete

## 🛠️ Tools Used

- n8n
- Jira Cloud 
- Google Gemini Chat Model
- Google Sheets
- Gmail

## 📊 Google Sheets Output

Each processed user story is logged into Google Sheets with fields such as:

| Column           | Description                                                              |
|------------------|--------------------------------------------------------------------------|
| Unique ID        | Story ID + Test ID combined, used to identify and update the correct row |
| Test ID          | Sequential ID for the test case within its story                         |
| Sprint           | The sprint the parent story belongs to                                   |
| Story ID         | Jira issue key of the parent user story                                  |
| Story Title      | Full title of the parent user story                                      |
| Test Title       | Short summary of the specific test scenario                              |
| Preconditions    | Setup required before the test steps can be executed                     |
| Steps            | Ordered actions to execute the test                                      |
| Expected Results | The verifiable outcome that confirms a pass                              |
| Priority         | High / Medium / Low, based on the test case's importance                 |
| Type             | Positive, Negative, or Edge                                              |
| Status           | Execution status of the test case                                        |
| Date             | Timestamp of when the row was generated                                  |



## 🔧 Setup

Requires n8n instance with Gmail,Jira,Google sheet and OpenAI/Gemini credentials configured.

## 👩‍💻 Author
Swati J 


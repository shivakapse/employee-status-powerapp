# 📋 Employee Weekly Status App

### A Canvas Power App for structured weekly employee reporting with secure OTP login and real-time Power BI dashboard visualization.

> Built by **Shiva Kapse** — Principal BI Engineer &nbsp;[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/shivakapse/)

---

## 🎯 Business Problem

Teams had no structured way to report weekly progress. Managers lacked visibility into blockers and goals — leading to delayed decisions and missed escalations.

---

## ✅ Solution Built

A full end-to-end Canvas Power App that allows employees to:

- 🔐 Log in securely via **OTP email authentication**
- 📝 Submit **structured weekly status updates**
- 👁️ View **personal submission history** and edit past entries
- 👥 Managers get a **Team View** of all team submissions
- 📊 Visualize team progress via an **embedded Power BI dashboard**
- 📧 Managers receive **automated email alerts** on every new submission

---

## 🔧 Features

| Feature | Details |
|---|---|
| 🔐 OTP Login | Secure entry — only registered employees can access the app |
| 📝 Weekly Status Form | What Achieved, Next Week Goals, Blockers, Support Needed |
| 🚨 Blocker Tracking | Flags support needed for immediate manager visibility |
| 📁 My Report Screen | Employee views and manages their own submission history |
| 👥 Team View Screen | Manager sees all team submissions, searchable by name |
| ✏️ Edit Record Screen | Employee can edit previously submitted entries |
| 📊 Power BI Dashboard | Visual summary — submissions per week, blockers, support needed |
| ⚡ Manager Alert Flow | Auto email to manager when new status is submitted |
| 🔒 Logout | Secure session clear on logout |

---

## 🛠️ Tech Stack

![Power Apps](https://img.shields.io/badge/Power%20Apps-742774?style=for-the-badge&logo=powerapps&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Power Automate](https://img.shields.io/badge/Power%20Automate-0066FF?style=for-the-badge&logo=powerautomate&logoColor=white)
![SharePoint](https://img.shields.io/badge/SharePoint-0078D4?style=for-the-badge&logo=microsoftsharepoint&logoColor=white)
![Outlook](https://img.shields.io/badge/Outlook-0078D4?style=for-the-badge&logo=microsoftoutlook&logoColor=white)

---

## 📐 App Architecture

```
┌─────────────────────────────────────────────────────┐
│               LOGIN SCREEN                          │
│  Full Name + Work Email → Send OTP → Verify & Sign  │
│  (OTP generated & emailed via Power Automate)       │
└──────────────────────┬──────────────────────────────┘
                       │
          ┌────────────▼────────────┐
          │   WEEKLY STATUS FORM    │
          │  • What Achieved        │
          │  • Next Week Goals      │
          │  • Blockers             │
          │  • Support Needed       │
          │  • Week Start Date      │
          └────────────┬────────────┘
                       │
         ┌─────────────▼──────────────┐
         │   SharePoint Lists         │
         │  • P1_EmployeeUsers        │
         │    (Email, OTP, OTPExpiry) │
         │  • P1_WeeklyStatus         │
         │    (All submission data)   │
         └──────┬──────────┬──────────┘
                │          │
    ┌───────────▼──┐  ┌────▼─────────────────┐
    │ Power        │  │ Power BI Dashboard    │
    │ Automate     │  │ • Submissions/Week    │
    │ Notify       │  │ • Support Needed %    │
    │ Manager      │  │ • Blockers/Week       │
    └──────────────┘  └───────────────────────┘
```

---

## 📸 Screenshots

### 01 — Login Screen
> Secure OTP-based authentication. Only registered employees can sign in.

![01 - Login Screen](screenshots/01_login_screen.png)

---

### 02 — OTP Email Received
> Employee receives a 6-digit One-Time Password in their Outlook inbox via Power Automate.

![02 - OTP Email](screenshots/04_otp_email.png)

---

### 03 — Weekly Status Form
> Employee fills in weekly achievements, next week goals, blockers, support needed, and week start date.

![03 - Weekly Status Form](screenshots/05_status_form_filled.png)

---

### 04 — My Report Screen
> Employee views their own submitted records. Each entry has a Delete 🗑️ and Edit ✏️ option.

![04 - My Report Screen](screenshots/06_my_report_data.png)

---

### 05 — Team View Screen
> Manager sees all team submissions in one place. Searchable by employee name.

![05 - Team View Screen](screenshots/07_team_view.png)

---

### 06 — Edit Record Screen
> Employee can update a previously submitted weekly status entry and save changes.

![06 - Edit Record Screen](screenshots/08_edit_record.png)

---

### 07 — Power BI Dashboard
> Real-time dashboard showing submissions per week, support needed %, blockers per week, and full detail table with date and employee slicers.

![07 - Power BI Dashboard](screenshots/09_powerbi_dashboard.png)

---

### 08 — Manager Email Alert
> Manager receives a structured email notification automatically when an employee submits their weekly status.

![08 - Manager Email Alert](screenshots/10_manager_email.png)

---

### 09 — SharePoint: P1_WeeklyStatus List
> All employee submissions are stored automatically in SharePoint by the app on form submit.

![09 - SharePoint WeeklyStatus](screenshots/11_sharepoint_weekly_status.png)

---

### 10 — SharePoint: P1_EmployeeUsers List
> Registered employee emails managed by Admin. OTP and OTPExpiry are auto-filled by Power Automate on each login.

![10 - SharePoint EmployeeUsers](screenshots/12_sharepoint_employee_users.png)

---

### 11 — Power Automate: P1_NotifyManager Flow
> Triggered automatically when a new item is created in P1_WeeklyStatus. Sends a formatted email to the manager.

![11 - Notify Manager Flow](screenshots/13_flow_notify_manager.png)

---

### 12 — Power Automate: P1_SendOTPEmail Flow
> Triggered from Power Apps on Send OTP click. Generates a 6-digit OTP, updates SharePoint, and emails it to the employee.

![12 - Send OTP Flow](screenshots/14_flow_send_otp.png)

---

## 🗂️ SharePoint Lists

### `P1_EmployeeUsers`
Stores registered employees allowed to log in. Managed by Admin.

| Column | Type | Description |
|---|---|---|
| Email | Single line of text | Employee work email — added manually by Admin |
| OTP | Number | Auto-filled by Power Automate on Send OTP |
| OTPExpiry | Date and Time | Auto-filled — OTP valid for 10 minutes |

### `P1_WeeklyStatus`
Stores all weekly submissions. Auto-filled by the app on form submit.

| Column | Type | Description |
|---|---|---|
| EmployeeName | Single line of text | Captured from login session |
| WeekStartDate | Date and Time | Selected by employee on form |
| WhatAchieved | Plain text (multiline) | This week's achievements |
| NextWeekGoals | Plain text (multiline) | Goals for next week |
| Blockers | Plain text (multiline) | Current blockers |
| SupportNeeded | Choice (Yes / No) | Flags if manager support is needed |
| SubmittedDate | Date and Time | Auto-set to Now() on submit |
| Email | Single line of text | Captured from login session |

> ⚠️ **Important:** `WhatAchieved`, `NextWeekGoals`, and `Blockers` columns must be set to **Plain text** in SharePoint — not Enhanced rich text — to avoid HTML being stored.

---

## 🖥️ App Screens

| # | Screen | Purpose |
|---|---|---|
| 1 | `LoginScreen` | OTP-based secure authentication |
| 2 | `StatusFormScreen` | Submit weekly status |
| 3 | `MyReportScreen` | View, edit, delete personal submissions |
| 4 | `TeamViewScreen` | Manager view of all team submissions |
| 5 | `EditRecordScreen` | Edit a previously submitted record |

---

## ⚡ Power Automate Flows

### `P1_SendOTPEmail`
Triggered from Power Apps when employee clicks **Send OTP**.

```
When Power Apps calls a flow (V2)
        ↓
Initialize variable (random 6-digit OTP)
        ↓
Get items from P1_EmployeeUsers (filter by email)
        ↓
For each → Update item (save OTP + OTPExpiry)
        ↓
Send an email (V2) — deliver OTP to employee inbox
        ↓
Respond to Power App (return OTP value)
```

### `P1_NotifyManager`
Triggered automatically when a new item is created in `P1_WeeklyStatus`.

```
When an item is created (P1_WeeklyStatus)
        ↓
Send an email (V2) — formatted status summary to manager
```

---

## 🔑 Key Power Apps Formulas

### Send OTP — LoginScreen
```powerfx
If(
    IsBlank(txtName.Text),
    Notify("Please enter your full name.", NotificationType.Warning),
    IsBlank(txtEmail.Text),
    Notify("Please enter your work email.", NotificationType.Warning),
    IsBlank(LookUp(P1_EmployeeUsers, Email = txtEmail.Text)),
    Notify("This email is not registered.", NotificationType.Error),
    Set(varEmployeeName, txtName.Text);
    Set(varUserEmail, txtEmail.Text);
    Set(varOTPResult, P1_SendOTPEmail.Run(txtEmail.Text));
    Set(varOTP, varOTPResult.otpvalue);
    Notify("OTP sent to " & txtEmail.Text & ". Please check your inbox.", NotificationType.Success)
)
```

### Verify & Sign In — LoginScreen
```powerfx
Set(varUser, Last(Sort(Filter(P1_EmployeeUsers, Email = txtEmail.Text), ID, SortOrder.Ascending)));
If(
    IsBlank(varOTP), Notify("Please send OTP first.", NotificationType.Warning),
    IsBlank(txtOTP.Text), Notify("Please enter the OTP.", NotificationType.Warning),
    Value(Trim(txtOTP.Text)) = Value(varOTP),
    Set(varUserEmail, txtEmail.Text);
    Set(varEmployeeName, txtName.Text);
    Reset(txtName); Reset(txtEmail); Reset(txtOTP);
    Set(varOTP, Blank());
    Navigate(StatusFormScreen, ScreenTransition.Fade),
    Notify("Invalid OTP. Please try again.", NotificationType.Error)
)
```

### Submit Status — StatusFormScreen
```powerfx
Patch(
    P1_WeeklyStatus,
    Defaults(P1_WeeklyStatus),
    {
        Email: varUserEmail,
        EmployeeName: varEmployeeName,
        WeekStartDate: DataCardValue7.SelectedDate,
        WhatAchieved: DataCardValue9.Text,
        NextWeekGoals: DataCardValue10.Text,
        Blockers: DataCardValue11.Text,
        SupportNeeded: {Value: DataCardValue12.Selected.Value},
        SubmittedDate: Now()
    }
);
NewForm(frmWeeklyStatus);
Notify("Status submitted successfully ✅", NotificationType.Success);
Navigate(MyReportScreen, ScreenTransition.Fade)
```

### Logout — All Screens
```powerfx
Set(varUserEmail, Blank());
Set(varEmployeeName, Blank());
Set(varOTP, Blank());
Navigate(LoginScreen, ScreenTransition.Fade)
```

---

## ⚠️ Known Fixes Applied During Development

| # | Fix | Description |
|---|---|---|
| 1 | OTP Expiry | Removed strict expiry check — use `addMinutes(utcNow(), 10)` in flow |
| 2 | Form Reset | Use `NewForm(frmWeeklyStatus)` after submit instead of `ResetForm()` |
| 3 | HTML in Fields | Changed `WhatAchieved`, `NextWeekGoals`, `Blockers` to Plain text in SharePoint |
| 4 | Dropdown Fix | Set `Items = ["Yes","No"]` and `SearchEnabled = false` on Support Needed dropdown |
| 5 | My Report Filter | Gallery `Items = Filter(P1_WeeklyStatus, Email = varUserEmail)` |

---

## 🌱 More Apps Coming

- 🔜 App 2 — In Progress
- 🔜 App 3 — In Progress

---

*Built on Microsoft Power Platform · SharePoint · Power BI · Outlook*

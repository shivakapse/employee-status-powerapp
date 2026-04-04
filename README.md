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
- 👥 Managers get a **Team View** of all submissions
- 📊 Visualize team progress via an **embedded Power BI dashboard**
- 📧 Managers receive **automated email alerts** on new submissions

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

## 🗂️ SharePoint Lists

### `P1_EmployeeUsers`
Stores registered employees allowed to log in. Managed by Admin.

| Column | Type | Description |
|---|---|---|
| Email | Single line of text | Employee work email (added by Admin) |
| OTP | Number | Auto-filled by Power Automate on Send OTP |
| OTPExpiry | Date and Time | Auto-filled — OTP valid for 10 minutes |

### `P1_WeeklyStatus`
Stores all weekly submissions. Auto-filled by the app on form submit.

| Column | Type | Description |
|---|---|---|
| EmployeeName | Single line of text | From login session variable |
| WeekStartDate | Date and Time | Selected by employee |
| WhatAchieved | Plain text (multiline) | This week's achievements |
| NextWeekGoals | Plain text (multiline) | Goals for next week |
| Blockers | Plain text (multiline) | Current blockers |
| SupportNeeded | Choice (Yes / No) | Flags if manager support is needed |
| SubmittedDate | Date and Time | Auto-set to Now() on submit |
| Email | Single line of text | From login session variable |

> ⚠️ **Important:** `WhatAchieved`, `NextWeekGoals`, and `Blockers` must be set to **Plain text** (not Enhanced rich text) to avoid HTML being saved.

---

## 🖥️ App Screens

| Screen | Purpose |
|---|---|
| `LoginScreen` | OTP-based authentication |
| `StatusFormScreen` | Submit weekly status |
| `MyReportScreen` | View / Delete personal submissions |
| `TeamViewScreen` | Manager view — all team submissions |
| `EditRecordScreen` | Edit a previously submitted record |

---

## ⚡ Power Automate Flows

### `P1_SendOTPEmail`
Triggered from Power Apps when employee clicks **Send OTP**.

```
When Power Apps calls a flow (V2)
        ↓
Initialize variable (OTP = random 6-digit number)
        ↓
Get items (P1_EmployeeUsers — filter by email)
        ↓
For each (update OTP + OTPExpiry = addMinutes(utcNow(), 10))
        ↓
Send an email (V2) — OTP to employee inbox
        ↓
Respond to Power App (return OTP value)
```

### `P1_NotifyManager`
Triggered automatically when a new item is created in `P1_WeeklyStatus`.

```
When an item is created (P1_WeeklyStatus)
        ↓
Send an email (V2) — notify manager with submission details
```

---

## 🔑 Key Power Apps Formulas

### Send OTP (LoginScreen)
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

### Verify & Sign In (LoginScreen)
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

### Submit Status (StatusFormScreen)
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

### Logout (All Screens)
```powerfx
Set(varUserEmail, Blank());
Set(varEmployeeName, Blank());
Set(varOTP, Blank());
Navigate(LoginScreen, ScreenTransition.Fade)
```

---

## 📊 Power BI Dashboard

Connected to `P1_WeeklyStatus` SharePoint list. Includes:

- 📊 **Submissions Per Week** — Bar chart by WeekStartDate
- 🍩 **Support Needed** — Donut chart (Yes vs No %)
- 📊 **Blockers Per Week** — Bar chart showing active blocker count
- 📋 **Detail Table** — Full employee submission data
- 🔍 **Slicers** — Filter by Week Start Date range and Employee Name

---

## ⚠️ Known Fixes Applied

| Fix | Description |
|---|---|
| OTP Expiry | Removed strict expiry check — fix `addMinutes(utcNow(), 10)` in flow for proper expiry |
| Form Reset | Use `NewForm(frmWeeklyStatus)` after submit instead of `ResetForm()` |
| HTML in fields | Changed `WhatAchieved`, `NextWeekGoals`, `Blockers` from Enhanced rich text → Plain text in SharePoint |
| Dropdown | Set `Items = ["Yes", "No"]` and `SearchEnabled = false` on Support Needed dropdown |
| My Report Filter | Gallery filtered by `Filter(P1_WeeklyStatus, Email = varUserEmail)` |

---

## 🌱 More Apps Coming

- 🔜 App 2 — In Progress
- 🔜 App 3 — In Progress

---

*Built on Microsoft Power Platform · SharePoint · Power BI*

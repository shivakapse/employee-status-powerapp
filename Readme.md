```markdown
# 📋 Employee Weekly Status App  

A Canvas Power App designed to standardize weekly reporting, improve visibility for managers, and enable data-driven tracking through Power BI.

> Built by **Shiva Kapse** — Principal BI Engineer  
[LinkedIn](https://www.linkedin.com/in/shivakapse/)

---

## 🎯 Business Problem  

Lack of a structured process for weekly reporting resulted in limited visibility into team progress, blockers, and upcoming goals. This led to delays in decision-making and missed escalations.

---

## ✅ Solution  

Developed an end-to-end Canvas Power App to streamline weekly reporting and provide real-time insights.

**Key capabilities:**
- Secure login using OTP-based authentication  
- Structured weekly status submission  
- Personal history tracking with edit capability  
- Manager-level team view  
- Embedded Power BI dashboard for insights  
- Automated email notifications on submission  

---

## 🔧 Features  

- OTP-based secure login (registered users only)  
- Weekly status form (Achievements, Goals, Blockers, Support Needed)  
- Blocker tracking for quick visibility  
- My Report screen (view/edit/delete submissions)  
- Team View screen for managers  
- Power BI dashboard integration  
- Automated manager notification via Power Automate  
- Secure logout functionality  

---

## 🛠️ Tech Stack  

- Power Apps (Canvas App)  
- Power BI  
- Power Automate  
- SharePoint Online  
- Outlook  

---

## 📐 App Architecture  

```

Login Screen
→ Enter Name & Email
→ Send OTP (Power Automate)
→ Verify Login

↓

Weekly Status Form
→ Submit Achievements, Goals, Blockers

↓

SharePoint Lists

* P1_EmployeeUsers
* P1_WeeklyStatus

↓

Power Automate
→ OTP Email
→ Manager Notification

Power BI
→ Dashboard (Submissions, Blockers, Trends)

```

---

## 📸 Screenshots  

### Login Screen  
![Login Screen](Screenshots/1_login_screen.png)

### OTP Email  
![OTP Email](Screenshots/4_otp_email.png)

### Weekly Status Form  
![Weekly Status](Screenshots/5_status_form_filled.png)

### My Report Screen  
![My Report](Screenshots/6_my_report_data.png)

### Team View  
![Team View](Screenshots/7_team_view.png)

### Edit Record  
![Edit Record](Screenshots/8_edit_record.png)

### Power BI Dashboard  
![Dashboard](Screenshots/9_powerbi_dashboard.png)

### Manager Email  
![Manager Email](Screenshots/10_manager_email.png)

### SharePoint - Weekly Status  
![SharePoint Weekly](Screenshots/11_sharepoint_weekly_status.png)

### SharePoint - Users  
![SharePoint Users](Screenshots/12_sharepoint_employee_users.png)

### Notify Manager Flow  
![Flow Notify](Screenshots/13_flow_notify_manager.png)

### Send OTP Flow  
![Flow OTP](Screenshots/14_flow_send_otp.png)

---

## 🗂️ SharePoint Lists  

### P1_EmployeeUsers  

| Column | Type | Description |
|---|---|---|
| Email | Text | Managed by Admin |
| OTP | Number | Generated via flow |
| OTPExpiry | DateTime | Valid for 10 minutes |

---

### P1_WeeklyStatus  

| Column | Type | Description |
|---|---|---|
| EmployeeName | Text | From login |
| WeekStartDate | DateTime | Selected by user |
| WhatAchieved | Plain text | Weekly work |
| NextWeekGoals | Plain text | Upcoming work |
| Blockers | Plain text | Issues |
| SupportNeeded | Choice | Yes / No |
| SubmittedDate | DateTime | Auto |
| Email | Text | From login |

**Note:** Use Plain Text fields in SharePoint to avoid HTML storage issues.

---

## 🖥️ App Screens  

- LoginScreen — Authentication  
- StatusFormScreen — Submit status  
- MyReportScreen — Manage submissions  
- TeamViewScreen — Manager overview  
- EditRecordScreen — Update records  

---

## ⚡ Power Automate Flows  

### P1_SendOTPEmail  

```

Trigger: Power Apps
→ Generate OTP
→ Update SharePoint
→ Send Email
→ Return OTP

```

---

### P1_NotifyManager  

```

Trigger: New SharePoint Item
→ Send Email to Manager

````

---

## 🔑 Key Power Apps Formulas  

### Send OTP  
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
    Notify("OTP sent to " & txtEmail.Text, NotificationType.Success)
)
````

---

### Verify Login

```powerfx
Set(varUser, Last(Sort(Filter(P1_EmployeeUsers, Email = txtEmail.Text), ID, SortOrder.Ascending)));
If(
    Value(Trim(txtOTP.Text)) = Value(varOTP),
    Navigate(StatusFormScreen),
    Notify("Invalid OTP", NotificationType.Error)
)
```

---

### Submit Status

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
Navigate(MyReportScreen)
```

---

### Logout

```powerfx
Set(varUserEmail, Blank());
Set(varEmployeeName, Blank());
Navigate(LoginScreen)
```

---

## ⚠️ Known Fixes

* OTP expiry handled via Power Automate
* Used `NewForm()` for proper reset
* Converted rich text fields to plain text
* Simplified dropdown values
* Applied user-based filtering using email

---

## 🌱 More Apps

* App 2 — In Progress
* App 3 — In Progress

---

*Built using Microsoft Power Platform (Power Apps, Power Automate), SharePoint, and Power BI*

```
```

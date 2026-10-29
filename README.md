# 📶 Zippee WiFi Manager

A web app for managing the WiFi connections at all Zippee stores. For each connection it tracks the ISP login details, the payments and the renewal dates. It also handles disconnections and shifting, records where the routers are, and exports the credit-card recon file. It runs on **Supabase** and is a **single HTML file**.

<img width="1600" height="1590" alt="02-dashboard" src="https://github.com/user-attachments/assets/3012c83a-b947-4b43-ae8b-75e00f7dd055" />

---

## Contents
- [What it does](#what-it-does)
- [Screens](#screens)
- [Setup](#setup)
- [How renewal dates are calculated](#how-renewal-dates-are-calculated)
- [Daily email alerts](#daily-email-alerts)
- [CC recon export](#cc-recon-export)
- [Database](#database)
- [Troubleshooting](#troubleshooting)

---

## What it does

| Area | What you can do |
|---|---|
| **Dashboard** | Count of active WiFi, active stores, overdue, due in 3 days, this month's spend and pending disconnections. Charts for spend by payment method, ISP, city, brand, WiFi per store and renewal health. Every number follows the filters. |
| **Filters** | Brand, city, store, ISP and status, plus search by store, DSL ID, phone, name or email. Works on every page. |
| **Stores** | One card per store showing its WiFi connections (1, 2, 3, 4+). Open a store to see every connection's credentials and status. |
| **WiFi connections** | ISP, account / DSL ID, login method, login ID, password (hidden until you click), registered mobile, name and email, plan, last paid amount and date, tenure, next renewal. |
| **Login methods** | Set per ISP, and can be changed per connection: User ID + Password, Registered mobile + OTP, Account / DSL ID + Password, Email + Password, or Other. |
| **Renewals** | Overdue, due in 3 days or less, next 30 days, and no tenure set. Click **Mark paid** to record the payment and move the due date forward. |
| **Payments** | Record, edit or delete payments, filtered by date range and payment method. A **bulk bill** (one Airtel Business bill covering many stores) updates every connection it covers. |
| **Disconnect & shift** | Steps: requested → disconnected, or requested → shifted to another store or address. Records the ISP ticket number and keeps a history. |
| **Equipment** | Where each router, ONT and adapter is (at store, in storage, with a person, handed to ISP, lost), who holds it with their phone number, and whether the ISP has collected it. |
| **CC recon** | Choose a date range and download one Excel file with tabs for **ICICI**, **HDFC** and **Manish Bank**, in the same layout as the existing recon sheet. |
| **Editing** | Stores, connections, payments, equipment, ISPs and payment methods can all be edited. Duplicate stores can be merged. New store names fill in the brand and city automatically. |
| **Exports** | Excel export of connections, payments and renewals, plus a full backup. |
| **Alerts** | Daily 9 AM email listing overdue and due-soon renewals, sent by Google Apps Script. |
| **Access** | Supabase email login. Only signed-in team members can read or change data (Row Level Security). |

---

## Screens

### Login


Team members sign in with the email and password created in Supabase.

<img width="1600" height="960" alt="01-login" src="https://github.com/user-attachments/assets/b35ccdf8-aeef-42b8-befa-ff09c1de5616" />
### Dashboard: filtered by brand
Every number and chart updates for the selected brand, city, store or ISP. This example shows FNP.

<img width="1600" height="1590" alt="02-dashboard" src="https://github.com/user-attachments/assets/282ede1b-7dc6-4c52-aba5-6f89248822eb" />


### Stores
One card per store, with its WiFi connections. Red or orange tags mark connections that are overdue or due soon.

<img width="1600" height="960" alt="04-stores" src="https://github.com/user-attachments/assets/e942f8ae-d4ce-4d1d-a9d7-bfdf609e7245" />

### Store detail
Every connection at the store: credentials, registered contact, last payment, tenure, next renewal, equipment and history. Each connection has **Payment**, **Edit**, **Disconnect** and **Shift** buttons.

<img width="1600" height="960" alt="05-store-detail" src="https://github.com/user-attachments/assets/5e1e64f1-ec2d-4036-9289-bb51e9712bdf" />


### WiFi connections
Every connection in one table. The password stays hidden until you click 👁, and ⧉ copies it.

<img width="1600" height="960" alt="06-wifi-connections" src="https://github.com/user-attachments/assets/0ded2e2d-a1b1-4501-9cc2-2b249c2c9f0d" />

### Renewals
Grouped as Overdue / Due in 3 days or less / Next 30 days / All upcoming / No tenure set. Click **Mark paid** to record the renewal.

<img width="1600" height="960" alt="07-renewals" src="https://github.com/user-attachments/assets/5930aa94-8332-4d05-9cec-cfcb06a6d406" />

### Record payment
The amount, tenure and payment method are filled in from the last payment. Open **Bulk bill** to tick the other connections the same bill covers.

<img width="1600" height="960" alt="08-record-payment" src="https://github.com/user-attachments/assets/ee9d20d8-232a-4c1a-a9b7-0756e1c491ea" />

### Payments
Payments are filtered by date range and payment method, with the total shown.

<img width="1600" height="960" alt="09-payments" src="https://github.com/user-attachments/assets/5eefd18b-6464-4330-954a-aed669f79424" />


### Disconnect & equipment
Lists pending disconnections and shifts, equipment still waiting for ISP pickup, disconnected connections, and all equipment.

<img width="1600" height="960" alt="10-disconnect-equipment" src="https://github.com/user-attachments/assets/6cb17012-b8f2-4b61-9bba-9b19e24c0d25" />

### Disconnect a connection
Records the stage, date and ISP ticket, plus where the router and accessories are and who has them.

![Disconnect form](screenshots/disconnect-form.png)

### CC recon
Pick the statement date range, preview each tab with its total, and download the Excel file.

<img width="1600" height="960" alt="12-cc-recon" src="https://github.com/user-attachments/assets/43271ea0-8536-4709-815e-85eca9d998ec" />


### Settings
ISPs with their default login method and portal link. Payment methods and which ones are included in the recon export.

<img width="1600" height="960" alt="13-settings" src="https://github.com/user-attachments/assets/5107f894-0b59-49ca-804b-872f960272e1" />

### Mobile
The app works on phones too.

<img width="390" height="844" alt="14-mobile" src="https://github.com/user-attachments/assets/f9b74e90-688f-47d8-ae0f-2e9c4f16c065" />


---

## Setup

### 1. Supabase
1. In **SQL Editor**, run these in order:
   1. `00_cleanup_failed_run.sql`
   2. `01_schema.sql`
   3. `02_seed.sql` (optional, imports existing data; keep it private)
2. Go to **Authentication → Sign In / Providers → Email** and turn off **Allow new users to sign up**.
3. Go to **Authentication → Users → Add user → Create new user** and add each team member with an email and password.
4. Go to **Project Settings → API** and copy the **Project URL** and **anon public** key.

Every table, view and function is named `wifi_…`, so nothing clashes with tables from other dashboards in the same project.

### 2. App
1. Open `index.html` in a text editor and paste the URL and anon key into the `CONFIG` block near the bottom. The delivered copy already has them.
2. Host the file on Netlify Drop, Vercel or GitHub Pages, or open it directly in a browser.
   - **GitHub Pages:** in this repo go to **Settings → Pages → Deploy from a branch → `main` / root**. The app will be at `https://<your-username>.github.io/<repo-name>/`.

> The **anon** key is safe to put in the page, because Row Level Security only lets signed-in users read or write data. **Never** put the `service_role` key in `index.html`.

### 3. Email alerts
See [Daily email alerts](#daily-email-alerts).

---

## How renewal dates are calculated

```
next due = last payment date + tenure (months)
```

- The tenure comes from the latest payment. If that payment has no tenure, the connection's **renewal tenure** is used.
- If **Manual next due date** is set on a connection, it's used until the next payment is recorded.
- Due dates are worked out in Indian time (IST).
- Status: **Overdue** (date has passed), **Due ≤ 3 days**, **OK**, or **No tenure set**.
- You don't need to mark anything **PAID** as you did in the sheet. Recording the payment moves the date forward.

---

## Daily email alerts

`alerts_apps_script.gs` replaces the old sheet-based script. It reads due dates from Supabase and sends the same alert email.

1. In Apps Script, paste the file over the old script.
2. Go to **Project Settings → Script Properties** and add:
   - `SUPABASE_URL`: `https://<project>.supabase.co`
   - `SUPABASE_SERVICE_KEY`: the **service_role** key. Keep it only here.
3. Run `setupTriggers()` once to schedule it daily at 9 AM, then run `checkAndAlert()` once to test.

Recipients are set at the top of the script with `PRIMARY_EMAIL`, `CC_EMAILS` and `DAYS_BEFORE_ALERT`.

---

## CC recon export

**CC Recon** builds the same workbook as the existing *Credit Card Recon* file:

| Date | Entity | Particulars | Qty | Price | Amount |
|---|---|---|---|---|---|
| 17.09.2026 | FKM | ZD FKM Poojapura BSNL Recharge | 1 | ₹1,106 | ₹1,106 |
| … | | | | | |
| **Total Amount** | | | | | `=SUM(F…)` |

- There's one tab per payment method: `Wifi (ICICI)`, `Wifi (HDFC)` and `Wifi (Manish Bank)`. Tab names and which methods are included can be changed in **Settings → Payment methods**.
- **Entity** is the store's brand. **Particulars** is *store + ISP + type*. Both can be overridden on each payment.
- The file uses Arial, thin borders, ₹ number formats, the green Amount column and a live `SUM` total.

---

## Database

| Object | Holds |
|---|---|
| `wifi_stores` | Store name, brand, city, address, POC, status |
| `wifi_connections` | One row per WiFi: ISP, account / DSL ID, login method, login ID and password, registered phone / name / email, plan, tenure, status |
| `wifi_payments` | Date, amount, tenure, type, method, recon entity / particulars, `covers_connection_ids` for bulk bills |
| `wifi_equipment` | Item, serial number, location, holder, whether the ISP collected it |
| `wifi_connection_events` | History of disconnections, shifts and reactivations, with ISP ticket numbers |
| `wifi_isps` | ISP list, default login method, portal URL, support contact |
| `wifi_payment_methods` | ICICI / HDFC / Manish Bank / Bank / Other, plus recon settings |
| `wifi_v_connections` | View: each connection with its store, last payment and next due date |
| `wifi_v_renewals` | View: live connections with days left and due status (used by the alert email) |
| `wifi_merge_stores()` | Merges a duplicate store into another one |

---

## Troubleshooting

| Problem | Cause and fix |
|---|---|
| **"Failed to fetch" on login** | The browser can't reach `*.supabase.co`. Open `https://<project>.supabase.co/auth/v1/health?apikey=<anon key>` in your browser. If the page doesn't load, your network or ISP is blocking Supabase: try mobile data, a VPN, or DNS `1.1.1.1`. If it shows text starting `{"version"…`, check the URL and key in `CONFIG`. |
| **"Invalid login credentials"** | The user doesn't exist or the password is wrong. Check under Authentication → Users. The user must have the **Email** provider. |
| **`column s.brand does not exist` while running SQL** | You're running an old copy of `01_schema.sql`. Use the `wifi_`-prefixed version, and run `00_cleanup_failed_run.sql` first. |
| **A store appears twice** | Use **Stores → Merge duplicates**. |
| **A connection has no due date** | Edit the connection and set **Renewal tenure (months)**, or record a payment with a tenure. |
| **The alert email doesn't arrive** | In Apps Script, open **Executions** to see the log, and check that both Script Properties are set. |

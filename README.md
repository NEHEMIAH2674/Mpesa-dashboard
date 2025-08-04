# 📊 Personal Finance Dashboard: MPESA  Tracker

This project extracts, transforms, and visualizes MPESA airtime transactions between **April 2023 – April 2025** using **Looker Studio**.

--

## 📁 Project Structure

mpesa-finance-dashboard/
├── data/
│ └── transactions.csv # Cleaned transaction data
├── notebooks/
│ └── mpesa_pdf_to_csv.ipynb # Google Colab notebook for PDF extraction
├── dashboard-design/
│ └── looker_screenshot.png # (Optional) Dashboard screenshot
├── requirements.txt # Python packages
└── README.md # You're here

## 🧰 Tools Used

- ✅ Google Colab / Python
- ✅ Looker Studio (formerly Google Data Studio)
- ✅ Google Sheets / CSV Upload
- ✅ MPESA PDF Statement

---

## ✅ Step 1: Extract Data from MPESA PDF

> Done in [`notebooks/mpesa_pdf_to_csv.ipynb`](notebooks/mpesa_pdf_to_csv.ipynb)

Install required libraries:

```python
!pip install PyPDF2 pdfplumber pandas openpyxl
Decrypt and unlock your MPESA statement:
import PyPDF2

input_pdf_path = "MPESA_Statement_2023-04-06_to_2025-04-06.pdf"
pdf_password = "your_password"

reader = PyPDF2.PdfReader(input_pdf_path)
if reader.is_encrypted:
    reader.decrypt(pdf_password)

writer = PyPDF2.PdfWriter()
for page in reader.pages:
    writer.add_page(page)

with open("unlocked_statement.pdf", "wb") as f:
    writer.write(f)

import pdfplumber
import pandas as pd

all_rows = []

with pdfplumber.open("unlocked_statement.pdf") as pdf:
    for page in pdf.pages:
        tables = page.extract_tables()
        for table in tables:
            if table and len(table[0]) >= 5:
                headers = table[0]
                for row in table[1:]:
                    if len(row) == len(headers):
                        all_rows.append(dict(zip(headers, row)))

df = pd.DataFrame(all_rows)
df.to_csv("transactions.csv", index=False)


✅ Step 2: Upload to Looker Studio
Go to Looker Studio

Create a new report

Add a data source:

Option 1: Upload transactions.csv directly to Google Drive and connect

Option 2: Upload the file to Google Sheets and connect

✅ Step 3: Create Calculated Fields in Looker Studio
🧮 Date Fields
Month:

sql
Copy
Edit
DATETIME_TRUNC(Completion_Time, MONTH)
Year:

YEAR(Completion_Time)
🔍 Transaction Type

CASE 
  WHEN Withdrawn > 0 THEN "Expense"
  WHEN Paid_In > 0 THEN "Income"
  ELSE "Other"
END
💸 Net Flow
sql
Copy
Edit
IFNULL(Paid_In, 0) - IFNULL(Withdrawn, 0)
✅ Step 4: Build Dashboard Sections
🔹 Summary Cards (Scorecards)
Total Spent: SUM(Withdrawn)

Total Income: SUM(Paid_In)

Net Flow: SUM(Paid_In) - SUM(Withdrawn)

Last Balance: Table sorted by Completion_Time DESC

🔹 Monthly Spending Trend
Chart: Time series

Dimension: Month

Metric: SUM(Withdrawn) and optionally SUM(Paid_In)

🔹 Spending by Category
Chart: Pie or Bar Chart

Dimension: Details_Breakdown

Metric: SUM(Withdrawn)

🔹 Transaction Table
Table View:

Fields: Completion_Time, Details, Paid_In, Withdrawn, Balance

🔹 Filters
Date Range

Details

Transaction Type

📦 requirements.txt
nginx
Copy
Edit
PyPDF2
pdfplumber
pandas
openpyxl
📸 Optional: Dashboard Preview


📎 License
This project is for personal use. You can fork or adapt it for your own finance tracking. Contributions welcome!



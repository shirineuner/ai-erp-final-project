# פרומפט בנייה ל-Lovable — אפליקציית ניהול ERP

## הפרומפט הראשוני (להדביק ב-Lovable)

Build an internal admin web app for a small Israeli electronics business
called "החנות האלקטרונית של פיסטוק".

REQUIREMENTS
- Hebrew UI, full RTL layout, ILS currency (₪), dates as dd/mm/yyyy.
- Clean, modern, light theme. Sidebar navigation on the right (RTL).
- Do NOT create your own database. All data comes from one REST endpoint
  that I will store as a secret named N8N_WEBHOOK_URL.

API CONTRACT
Every request is a POST to N8N_WEBHOOK_URL with a JSON body.
Three actions:

1) List records
   POST { "action": "list", "table": "Invoices" }
   -> returns a JSON array of records. Each record looks like:
      { "id": "rec...", "InvoiceNumber": "INV-0001", "CustomerId": "CUST-0001",
        "Amount": 1000, "VatAmount": 180, "Total": 1180,
        "Status": "הופקה", "PdfUrl": "https://...", "Created": "2026-09-28T..." }
   Valid table values: "Invoices", "Leads", "Products", "Tasks".

2) Create record
   POST { "action": "create", "table": "Leads",
          "payload": { "Name": "...", "Email": "...", "Phone": "...",
                       "Company": "...", "Status": "New" } }
   -> returns the created record.

3) Chat with the AI agent
   POST { "action": "chat", "message": "מה מדיניות ההחזרות?" }
   -> returns [{ "reply": "..." }]. Render the "reply" text.

SCREENS
- דשבורד: four stat cards — הכנסות החודש (sum of Total on Invoices),
  חשבוניות פתוחות (Status != "הופקה"), מספר לידים, לידים שהשיבו
  (Status = "Replied"). Below: a bar chart of leads by status.
- חשבוניות: table of Invoices. Columns: מספר, לקוח, סכום, מע"מ, סה"כ,
  סטטוס, מסמך (PdfUrl as a link "צפייה"). Search + filter by status.
- לידים: table of Leads. Columns: שם, אימייל, טלפון, חברה, סטטוס, נוצר.
  A "ליד חדש" button opening a form that POSTs action=create.
- מוצרים: table of Products. Columns: שם, קטגוריה, מחיר, במלאי (checkbox icon).
  Search by name and filter by category.
- משימות: table of Tasks. Columns: כותרת, סטטוס.
- צ'אט: a chat panel. User types, app POSTs action=chat, renders reply.
  Show a typing indicator while waiting.

RULES
- Store the endpoint as a secret. Never hardcode it in client code.
- Show a loading state on every fetch and a clear error message on failure.
- Format all money as ₪1,234.00.
- Empty tables show a friendly empty state, not a blank screen.

## שיפורים להמשך (אחרי הפרומפט הראשוני)

להוסיף אחד-אחד, לא הכל בבת אחת:

1. "הוסף סינון לפי סטטוס בטבלת החשבוניות"
2. "הפוך את הדשבורד לשתי עמודות"
3. "הוסף כפתור רענון בכל מסך"
4. "בצ'אט, הצג את ההודעות שלי מימין ואת התשובות משמאל"
5. "הוסף מיון לפי עמודה בכל הטבלאות"

## אבטחה

הכתובת של ה-webhook נשמרת כ-secret בצד השרת של Lovable, לא בקוד שרץ בדפדפן.
מפתח ה-Airtable לא נמצא באפליקציה כלל — כל הגישה לנתונים עוברת דרך n8n.

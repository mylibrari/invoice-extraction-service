## Invoice Extraction App

Invoice Extraction App

#### License

mit

## Clone the Repository

```bash
git clone https://github.com/mylibrari/invoice-extraction-service.git
cd invoice-extraction-service
```

installiation

bench get-app https://github.com/mylibrari/invoice-extraction-service

source env/bin/activate

pip install google-generativeai pillow requests python-dotenv

deactivate

or

bench setup requirements

bench --site [site_name] install-app invoice_extraction_app


get api key from google 

goto  https://site_name/app/gemini-settings/Gemini%20Settings
<img width="1170" height="568" alt="image" src="https://github.com/user-attachments/assets/ee7f96ba-7fa1-46ec-8e47-8af881152373" />


goto  https://site_name/app/extracted-invoice   

<img width="1162" height="634" alt="image" src="https://github.com/user-attachments/assets/a3b7524b-78ab-4d9f-8873-bae3e6631fca" />


add extracted invoice 

upload pdf/image file

press extract buttom 





Telegram bot

```md
# 📄 Invoice Extraction via Telegram Webhook (ERPNext)

A fully automated system to extract invoice data by sending files to a Telegram bot.  
Invoices are received via **Telegram Webhook**, stored in **ERPNext**, processed using **Gemini AI**, and saved as **Extracted Invoice** documents.

---

## 🚀 Features

- Send invoice files (PDF / JPG / PNG) via Telegram
- Automatic file saving in ERPNext File Manager
- Automatic creation of **Extracted Invoice**
- AI-based data extraction (supplier, items, tax, totals)
- Webhook-based (no polling, no manual buttons)
- Ready to convert into Purchase Invoice

---

## 🧱 Requirements

- ERPNext / Frappe Framework
- App: `invoice_extraction_app`
- Telegram Bot
- Public HTTPS URL (ngrok or real domain)
- Gemini API Key

---

## 1️⃣ Create Telegram Bot

1. Open Telegram
2. Search for **@BotFather**
3. Run:
```

/newbot

```
4. Save the **Bot Token**

---

## 2️⃣ Get Chat ID

Open in browser:
```

[https://api.telegram.org/bot](https://api.telegram.org/bot)<BOT_TOKEN>/getUpdates

````

Copy the value of:
```json
chat.id
````

---

## 3️⃣ Configure Telegram Settings in ERPNext

Go to:

```
ERPNext → Telegram Settings
```

Fill the fields:

| Field                       | Value                                                      |
| --------------------------- | ---------------------------------------------------------- |
| Enable Telegram Integration | ✔ Enabled                                                  |
| Bot Token                   | Your Bot Token                                             |
| Admin Chat ID               | Your Chat ID                                               |
| Public Base URL             | [https://xxxx.ngrok-free.dev](https://xxxx.ngrok-free.dev) |
| Webhook URL                 | Auto-generated                                             |

---

## 4️⃣ Start ngrok

Expose ERPNext HTTPS port:

```bash
ngrok http 443
```

Copy the generated public URL:

```
https://xxxx.ngrok-free.dev
```

Paste it into **Public Base URL** in Telegram Settings.

---

## 5️⃣ Activate Telegram Webhook (Recommended)

Run this command on the server:

```bash
bench --site YOUR_SITE execute invoice_extraction_app.telegram.setup_webhook_ui
```

Expected result:

```json
{
  "ok": true,
  "description": "Webhook was set"
}
```

Webhook URL format:

```
https://YOUR_PUBLIC_DOMAIN/api/method/invoice_extraction_app.telegram.webhook
```

---

## 6️⃣ Send Invoice via Telegram

* Open your Telegram bot
* Send:

  * PDF invoice
  * or Image invoice

📌 What happens automatically:

* File is saved in File Manager
* Extracted Invoice is created
* original_file field is set
* Invoice data is extracted
* Status becomes `Ready`

---

## 7️⃣ Result in ERPNext

✔ New document created:

```
Extracted Invoice
EXT-TG-00001
```

✔ File attached
✔ Items extracted
✔ Tax calculated
✔ Ready for Purchase Invoice creation

---

## 8️⃣ Common Issues & Solutions

| Issue                         | Solution             |
| ----------------------------- | -------------------- |
| Webhook not working           | Re-run setup_webhook |
| Invoice created without data  | Check Gemini API key |
| Mandatory original_file error | File download failed |
| Bot not responding            | Verify Chat ID       |
| ngrok URL changed             | Re-set webhook       |

---

## 🔐 Security Notes

* Admin Chat ID restricts access to one chat
* Webhook accepts only Telegram IPs
* No polling = better performance

---

## 📦 Production Notes

* ngrok URLs change on restart (free plan)
* For production, use a real domain
* Webhook must always point to HTTPS

---

## ✅ System Status

✔ Webhook only
✔ No polling
✔ No manual UI dependency
✔ Fully automated

---

**Built for ERPNext automation 🚀**

```
```

curl -X POST "https://api.telegram.org/bot<BOT_TOKEN>/setWebhook" \
  -d "url=https://YOUR_PUBLIC_DOMAIN/api/method/invoice_extraction_app.telegram.webhook"

curl "https://api.telegram.org/bot<BOT_TOKEN>/getWebhookInfo"


bench --site [site_name] execute invoice_extraction_app.telegram.setup_webhook
bench --site [site_name] execute invoice_extraction_app.telegram.webhook_info
bench --site [site_name] execute invoice_extraction_app.telegram.disable_webhook


frappe.ui.form.on("Telegram Settings", {
  refresh(frm) {
    frm.add_custom_button("Activate Webhook", async () => {
      const r = await frappe.call({
        method: "invoice_extraction_app.telegram.setup_webhook",
        freeze: true
      });
      frappe.msgprint({ title: "Result", message: "<pre>" + JSON.stringify(r.message || {}, null, 2) + "</pre>" });
      frm.reload_doc();
    });

    frm.add_custom_button("Disable Webhook", async () => {
      const r = await frappe.call({
        method: "invoice_extraction_app.telegram.disable_webhook",
        args: { drop_pending_updates: 0 },
        freeze: true
      });
      frappe.msgprint({ title: "Result", message: "<pre>" + JSON.stringify(r.message || {}, null, 2) + "</pre>" });
      frm.reload_doc();
    });

    frm.add_custom_button("Webhook Info", async () => {
      const r = await frappe.call({
        method: "invoice_extraction_app.telegram.webhook_info",
        freeze: true
      });
      frappe.msgprint({ title: "Webhook Info", message: "<pre>" + JSON.stringify(r.message || {}, null, 2) + "</pre>" });
    });
  }
});

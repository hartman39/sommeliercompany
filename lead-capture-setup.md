# Lead Capture Implementation Guide

## ⚠️ **Important: Leads Are Not Currently Being Saved!**

The forms on the website currently don't save lead data anywhere. You need to implement one of these solutions:

## 🚀 **Quick Setup Options (Choose One):**

### **Option 1: Formspree (Easiest - 5 minutes)**
1. Go to https://formspree.io
2. Sign up for free account (50 submissions/month free)
3. Create a new form
4. Get your form ID (looks like: `f/xyzabc`)
5. Update these files with your form ID:

**In `sommelier-company-mvp.html`:**
```javascript
// Replace line ~1250
const response = await fetch('https://formspree.io/f/YOUR_FORM_ID', {
    method: 'POST',
    headers: {'Content-Type': 'application/json'},
    body: JSON.stringify(formData)
});
```

**In `sommelier-booking-wizard-v2.html`:**
```javascript
// Add after line ~1444 (in submitBooking function)
await fetch('https://formspree.io/f/YOUR_FORM_ID', {
    method: 'POST',
    headers: {'Content-Type': 'application/json'},
    body: JSON.stringify(bookingData)
});
```

### **Option 2: Google Sheets (Free - 15 minutes)**
1. Create a Google Sheet for leads
2. Go to Extensions → Apps Script
3. Use this script:

```javascript
function doPost(e) {
  const sheet = SpreadsheetApp.getActiveSheet();
  const data = JSON.parse(e.postData.contents);

  sheet.appendRow([
    new Date(),
    data.firstName,
    data.lastName,
    data.email,
    data.phone,
    data.eventType,
    data.eventDate,
    data.guestCount,
    data.location,
    data.company
  ]);

  return ContentService
    .createTextOutput(JSON.stringify({result: 'success'}))
    .setMimeType(ContentService.MimeType.JSON);
}
```

4. Deploy as Web App
5. Copy the URL and add to your forms

### **Option 3: Zapier (Most Flexible - 10 minutes)**
1. Sign up at https://zapier.com (free tier available)
2. Create a Webhook trigger
3. Connect to your preferred destination:
   - Email (Gmail, Outlook)
   - CRM (HubSpot, Salesforce)
   - Spreadsheet (Google Sheets, Excel)
   - Database (Airtable, Notion)
4. Get webhook URL
5. Add to forms:

```javascript
await fetch('https://hooks.zapier.com/hooks/catch/YOUR_ID/YOUR_KEY/', {
    method: 'POST',
    body: JSON.stringify(formData)
});
```

### **Option 4: Email Notification (Temporary Solution)**
Use EmailJS for direct email notifications:

1. Sign up at https://www.emailjs.com (200 emails/month free)
2. Create email service and template
3. Add to your site:

```html
<script src="https://cdn.jsdelivr.net/npm/@emailjs/browser@3/dist/email.min.js"></script>
<script>
    emailjs.init("YOUR_PUBLIC_KEY");

    // In form submit:
    emailjs.send("service_id", "template_id", formData);
</script>
```

## 📊 **Data Being Captured:**

### Main Landing Form:
- Event Type
- Event Date
- Guest Count
- Location

### Booking Wizard Form:
- First Name
- Last Name
- Email
- Phone
- Company (optional)
- Event Details
- Wine Preferences
- Special Requests
- Total Quote Amount

### Exit Intent Popups:
- Email only

## 🔒 **Security Considerations:**

1. **Never expose API keys** in frontend code
2. **Use HTTPS** for all form submissions
3. **Add reCAPTCHA** to prevent spam:
```html
<script src="https://www.google.com/recaptcha/api.js"></script>
<div class="g-recaptcha" data-sitekey="YOUR_SITE_KEY"></div>
```

4. **Validate emails** server-side
5. **Add rate limiting** to prevent abuse

## 📈 **Lead Tracking Setup:**

### Google Analytics Events (Already Implemented):
```javascript
gtag('event', 'generate_lead', {
    'currency': 'USD',
    'value': estimated_value
});
```

### Facebook Pixel (Add to pages):
```html
<!-- Facebook Pixel Code -->
<script>
!function(f,b,e,v,n,t,s)
{if(f.fbq)return;n=f.fbq=function(){n.callMethod?
n.callMethod.apply(n,arguments):n.queue.push(arguments)};
if(!f._fbq)f._fbq=n;n.push=n;n.loaded=!0;n.version='2.0';
n.queue=[];t=b.createElement(e);t.async=!0;
t.src=v;s=b.getElementsByTagName(e)[0];
s.parentNode.insertBefore(t,s)}(window,document,'script',
'https://connect.facebook.net/en_US/fbevents.js');

fbq('init', 'YOUR_PIXEL_ID');
fbq('track', 'PageView');
fbq('track', 'Lead');
</script>
```

## 🚨 **Immediate Action Required:**

1. **Choose one of the options above**
2. **Implement within 24 hours** to avoid losing leads
3. **Test the form submission** thoroughly
4. **Set up email notifications** for new leads
5. **Create a backup** (Google Sheets recommended)

## 📧 **Recommended Lead Flow:**

1. User fills form →
2. Lead saved to database/spreadsheet →
3. Email notification to sales team →
4. Auto-response email to customer →
5. CRM entry created →
6. Follow-up sequence triggered

## 💡 **Pro Tips:**

- Start with Formspree or Google Sheets for immediate capture
- Add Zapier later for advanced automation
- Set up email notifications so you never miss a lead
- Export leads daily until permanent solution is in place
- Consider using multiple methods for redundancy

---

**⚠️ Without implementing one of these solutions, all form submissions are being lost!**
# Integrate OpenELIS with Odooco: My GSOC 2025 Journey with OpenELIS and Odoo

## 🚀 Introduction

This summer, I had the opportunity to work on an exciting project as part of **Google Summer of Code (GSOC) 2025**. My project focused on bridging two powerful open-source systems **OpenELIS Global**, a Laboratory Information Management System (LIMS), and **Odoo**, a comprehensive business management platform.

- Mentor: [@reagan-meant](https://github.com/reagan-meant) 
- Backup Mentor: [@mozzy11](https://github.com/mozzy11)
- 💻 Explore the [code commits](https://github.com/DIGI-UW/OpenELIS-Global-2/commits?author=vickabire)

The mission?
👉 To automate billing workflows, so that invoices are seamlessly generated in Odoo whenever lab orders are placed in OpenELIS.

---

## ❌ The Problem: Manual Billing

For many laboratories, billing is still a **manual and error-prone process**. Staff often re-enter patient and test data from OpenELIS into Odoo.

⚠️ Problems with the old workflow:

* Repetitive data entry 📝
* Increased errors ❌
* Slower turnaround ⏳
* Poor financial visibility 💸

---

## 🏗 Designing the Integration

To solve this, I designed a **Service-Oriented Architecture (SOA)** with a clear separation of concerns:

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   OpenELIS      │───▶│   Integration    │───▶│      Odoo       │
│   (LIMS)        │    │     Service      │    │   (ERP/CRM)     │
└─────────────────┘    └──────────────────┘    └─────────────────┘
```

* **OpenELIS** → manages lab tests & workflows
* **Integration Service** → event-driven bridge between systems
* **Odoo** → handles billing & financial records

---

## 🧩 Core Components

1. **OdooClient** – Handles XML-RPC communication (auth, CRUD, invoices)
2. **OdooIntegrationService** – Business logic for invoice creation
3. **TestProductMapping** – CSV-based test-to-product mapping
4. **Event Handling** – Uses Spring events for asynchronous processing

---

## ⚙️ Building the Odoo Client Layer

✨ Key capabilities:

* 🔑 **Authentication** with Odoo’s XML-RPC endpoint
* 👤 **Automatic patient creation** in Odoo if they don’t exist
* 🔍 **Duplicate prevention** using national IDs or names
* 🛡️ **Error resilience** – failures in Odoo don’t stop OpenELIS operations

---

## 📊 Mapping Tests to Products

At the heart of the integration is a simple **CSV configuration file**:

```csv
loinc_code,product_name,quantity,price_unit
12345-6,Complete Blood Count,1,25.00
78901-2,Basic Metabolic Panel,1,35.00
```

✅ Laboratories can update test pricing or product mappings anytime without touching code.

---

## 🔄 Event-Driven Workflow

Here’s how it works step by step:

```
Sample Created in OpenELIS  
           │  
           ▼  
[ Event Fired: SamplePatientUpdateDataCreated ]  
           │  
           ▼  
OdooIntegrationService → Finds/creates patient in Odoo  
           │  
           ▼  
Maps lab tests → Odoo products  
           │  
           ▼  
💰 Invoice automatically created in Odoo  
```

---

## 🖥 Deployment & Monitoring

Configuration is externalized via environment variables: Look at this example of a repository that i created to run the integration, all i need to do is build openELIS with the Odoo changes and then copy the `.war` to `config/openelis/tomcat/war`. 
https://github.com/vickabire/odoo-openelis-connector
🔎 Health check endpoint `/health/odoo` returns:

```json
{
  "status": "UP",
  "odoo": "Available"
}
```

---

## 🌟 The Benefits

With this integration in place, laboratories now enjoy:

* ⚡ **Automated Billing** – invoices created instantly
* ✅ **Data Consistency** – no mismatched records
* ⏱️ **Operational Efficiency** – less admin work
* 📈 **Real-Time Financial Visibility** – track revenue seamlessly
* 🔮 **Scalability** – flexible SOA design for future features

---

## 🙌 Reflections

Working on this project was both technically challenging and deeply rewarding. I had the privilege of being mentored by [reagan-meant](https://github.com/reagan-meant), whose guidance made this journey smoother and more impactful.

## :video_camera: Demo
https://youtu.be/Tf-BQ3L93kM?si=fl19tyrbFDv5Ag49

## 🚀 Final Thoughts

My GSOC 2025 journey has been an incredible learning experience. More importantly, it’s a step toward making healthcare systems more **efficient, reliable, and sustainable** through automation and integration.

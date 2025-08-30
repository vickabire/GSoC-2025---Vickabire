# GSoC 2025: Integrate OpenELIS with Odoo - Final Presentation
* **Primary Mentor**: [@reagan-meant](https://github.com/reagan-meant)
* **Backup Mentor**: [@mozzy](https://github.com/mozzy11)

This summer, I had the opportunity to work on an exciting project as part of **Google Summer of Code (GSOC) 2025**. My project focused on bridging two powerful open-source systems—**OpenELIS Global**, a Laboratory Information Management System (LIMS), and **Odoo**, a comprehensive business management platform. The goal was to automate billing workflows, making it easier for laboratories to seamlessly generate invoices in Odoo whenever lab orders are placed in OpenELIS.

For many laboratories, billing is still a manual and time-consuming process. Staff often have to enter patient and test information into Odoo after the tests are ordered in OpenELIS, which not only consumes time but also creates room for errors and inconsistencies. My project aimed to eliminate this gap by creating an integration layer between the two systems. The integration ensures that invoices are automatically generated in Odoo with the correct patient and test details as soon as lab orders are created in OpenELIS.

## Designing the Integration
To achieve this, I designed the integration using a Service-Oriented Architecture (SOA). OpenELIS handles laboratory workflows, Odoo manages the business and financial operations, and the integration service sits in between, connecting the two systems. This service relies on the Spring Framework for event-driven processing and uses XML-RPC to communicate with Odoo.

Each component of the architecture plays a specific role:

- The OdooClient manages authentication and requests.

- The OdooIntegrationService implements the business logic for creating invoices.

- The CSV-based TestProductMapping defines how lab tests correspond to Odoo products.

### Solution Architecture

The integration follows a **Service-Oriented Architecture (SOA)** pattern with clear separation of concerns:

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   OpenELIS      │    │   Integration    │    │      Odoo       │
│   (LIMS)        │───▶│     Service      │───▶│   (ERP)         │
│                 │    │                  │    │                 │
└─────────────────┘    └──────────────────┘    └─────────────────┘
```

## Building the Odoo Client Layer
One of the most rewarding parts of the project was building the Odoo client layer. This component handles authentication, customer management, and invoice creation. For example, if a patient exists in Odoo, the client fetches their record; if not, a new customer profile is created automatically. Duplicate entries are avoided by checking identifiers like national IDs or names. On top of this, the system gracefully handles errors so that any failure in Odoo does not disrupt OpenELIS operations.

## Mapping Tests to Products
The integration relies heavily on a simple but powerful configuration file: a CSV mapping file that links laboratory test codes (LOINC) to Odoo product definitions. Each row specifies the test code, product name, quantity, and price. This allows laboratories to easily update pricing or product mappings without changing the code. For example, a “Complete Blood Count” test with LOINC code 12345-6 can be mapped to a specific Odoo product with a price of $25.

## An Event-Driven Workflow
The workflow itself is entirely event-driven. Whenever a sample is created in OpenELIS, an event is triggered, which the integration service listens to. The service then looks up or creates the patient in Odoo, maps the ordered tests to products, and generates the invoice automatically. This architecture makes the process both efficient and reliable, while also ensuring that failures in Odoo do not affect the critical work of the laboratory.

## Deployment and Monitoring
From a deployment perspective, the integration is flexible and easy to configure. The Odoo connection details—such as base URL, database, username, and password—are stored as environment variables, keeping sensitive data secure and external to the codebase. The test-to-product mapping file is also externalized, meaning that updates to product definitions can be applied without redeploying the system.

To help administrators monitor the integration, a simple health check endpoint (/health/odoo) was implemented. This makes it easy to verify whether Odoo is available and responsive at any time.

Looking back, the integration project has delivered several clear benefits. Laboratories can now enjoy **automated billing**, reducing the manual workload for staff and eliminating redundant data entry. The system ensures **data consistency**, since patient and test information flows automatically between OpenELIS and Odoo. It also improves **operational efficiency**, gives real-time **financial visibility**, and lays a solid foundation for future scalability. Perhaps most importantly, the architecture was designed so that failures in Odoo never interfere with the laboratory’s core workflows in OpenELIS.

### Data Flow

#### 1. Sample Creation Workflow

```
Sample Created in OpenELIS
           │
           ▼
SamplePatientUpdateDataCreatedEvent
           │
           ▼
OdooIntegrationService.createInvoice()
           │
           ▼
Patient Partner Lookup/Creation
           │
           ▼
Test-to-Product Mapping
           │
           ▼
Invoice Creation in Odoo
```

This project was both technically challenging and deeply rewarding. I had the privilege of working with my mentor, [reagan-meant](https://github.com/reagan-meant), whose guidance was invaluable throughout the implementation. Together, we built a system that demonstrates how open-source solutions can be integrated to improve healthcare operations in real-world settings.

If you’d like to see the project in action, I’ve shared a [demo video](https://www.youtube.com/watch?v=Tf-BQ3L93kM&t=105s). You can also explore the [commits on GitHub](https://github.com/DIGI-UW/OpenELIS-Global-2/commits?author=vickabire) to dive into the technical details.

My GSOC 2025 journey has been an incredible learning experience, and I am excited about the impact this work can have in making healthcare systems more efficient and sustainable.

# 🧩 Shopify-Teable-MVP — *Project "TeableSync"*

---

## 🚀 Product Vision

**TeableSync** is a Shopify app that provides e-commerce owners with a powerful, spreadsheet-like interface (powered by **Teable**) for managing their entire catalog — featuring robust two-way synchronization, audit trails, and rollback capabilities.

**Core Value Proposition:**  
Combines the simplicity of a spreadsheet with the power of a database, directly synced with your Shopify store, eliminating the need for complex and expensive PIM systems for SMBs.

---

## 🧠 MVP Scope & Feature Prioritization

| **MVP Feature** | **Priority** | **Description** |
|------------------|--------------|-----------------|
| **1. Shopify App Installation & Auth** | 🔴 Critical | OAuth flow from Shopify App Store. Creates a unique Teable instance per store. |
| **2. Initial Product & Collection Import** | 🔴 Critical | One-click bulk import of all products, variants, images, and collections from Shopify into the store's dedicated Teable instance. |
| **3. Two-Way Sync (Basic)** | 🔴 Critical | **Teable → Shopify:** Manual “Push to Shopify” button.<br>**Shopify → Teable:** Near real-time sync via Shopify webhooks for changes made in the Shopify admin. |
| **4. Teable UI for Data Management** | 🔴 Critical | The store owner interacts directly with the hosted Teable instance to view, filter, and edit their product data in a grid. |
| **5. Basic Audit Log** | 🟠 High | A separate table within the Teable instance logs all sync operations (who, what, when). |
| **6. Simple Rollback** | 🟠 High | Ability to revert a product to its state before the last sync, using the audit log. |
| **7. Project Setup & DevOps Lite** | 🔴 Critical | Infrastructure-as-Code (e.g., Terraform) to automate the provisioning of a Teable instance + Neon DB for each new store. |

---

## 🏗️ Technical Architecture & Stack

### System Architecture Overview

![Architecture Diagram](https://github.com/user-attachments/assets/0bad56a7-192b-4017-b6eb-fc640daa1ba8)

---

### **1. Shopify App (Frontend)**

- **Technology:** Next.js (or Remix) for setup and onboarding UI  
- **Hosting:** Provided frontend servers  
- **Purpose:** Merchant-facing app interface inside Shopify Admin  

---

### **2. TeableSync Backend Service**

- **Technology:** Next.js API Routes (or Express.js)  
- **Hosting:** Provided backend servers (Node.js)  
- **Responsibilities:**
  - Handle Shopify OAuth
  - Receive and process Shopify webhooks
  - Manage Teable instance lifecycle (creation, configuration)
  - Orchestrate synchronization between Shopify and Teable

---

### **3. Teable Instances (Core Product)**

- **Technology:** Teable (self-hosted)
- **Hosting:** Provided servers — one instance per Shopify store
- **Database:** Each instance connects to its own dedicated **Neon PostgreSQL branch**

---

### **4. Main Application Database**

- **Technology:** Neon PostgreSQL  
- **Purpose:** Stores app-level data (not product data)  
  - Store configurations (shop_domain, access_token, teable_instance_url, teable_db_connection_string)  
  - Sync job history and audit metadata  
  - User sessions  

---

## ⚙️ Implementation Plan — *What It Will Take*

---

### **Phase 1: Foundation & Infrastructure (DevOps Lite)**

#### **Goal:** Achieve automated infrastructure provisioning and store onboarding.

**Tasks:**
- Define infrastructure using **Terraform** or **Pulumi**  
- Build a `create-store` script that, post-OAuth:
  - Creates a new PostgreSQL branch on Neon  
  - Spins up a new Teable Docker container  
  - Records the instance details in the main DB  

**Shopify App Setup:**
- Create the app in Shopify Partner Dashboard  
- Implement OAuth flow using Next.js or Express backend  

---

### **Phase 2: Core Data Synchronization**

**Import from Shopify:**
- Use Shopify Admin API (GraphQL) to fetch products, collections, variants, images, and metafields  
- Insert structured data into the Teable instance tables  
- Implement progress tracking for large imports  

**Data Mapping:**
- For MVP: Map core fields (title, description, price, SKU) to pre-defined Teable columns  
- Post-MVP: Advanced metafield mapping  

**Synchronization:**
- **Teable → Shopify:** “Push to Shopify” button triggers updates to Shopify  
- **Shopify → Teable:** Webhooks keep Teable updated in real time  

---

### **Phase 3: MVP Features & Polish**

**Audit Log:**
- Create an “Audit Log” table inside Teable  
- Log every sync action with metadata (timestamp, user, action, product_id, old_value, new_value)

**Rollback:**
- UI allows restoring a product to its previous version using audit logs  

**Onboarding:**
- Guided setup flow inside the app for initial sync and configuration  

---

## 🗓️ Project Timeline — *12 Weeks Total*

| **Milestone** | **Weeks** | **Goal** | **Deliverable** |
|----------------|-----------|-----------|------------------|
| **1. Foundation & Infrastructure** | 1–2 | Infrastructure setup & Shopify app skeleton | Working Shopify app installation that creates Teable instance per store |
| **2. Data Import & Teable Setup** | 3–4 | Import Shopify data into Teable | One-click import of entire Shopify catalog |
| **3. Core Synchronization** | 5–6 | Two-way sync | Reliable synchronization between Shopify ↔ Teable |
| **4. Audit & Rollback** | 7–8 | Implement audit trail and rollback | Complete audit and rollback system |
| **5. Polish & Integration** | 9–10 | UX, optimization, error handling | Production-ready, polished app |
| **6. Testing & Deployment** | 11–12 | QA, deployment, app submission | Live MVP in Shopify App Store |

---

## 🧱 Detailed Weekly Breakdown

### **Milestone 1: Foundation & Infrastructure (Weeks 1–2)**

**Week 1:**
- Set up development environment and repositories  
- Create Shopify Partner account and development store  
- Set up Neon.com database  
- Implement basic OAuth flow  
- Create Next.js app skeleton using Shopify Polaris  
- Set up Docker environment for local Teable testing  

**Week 2:**
- Implement Infrastructure as Code (Terraform)  
- Automate Teable instance creation  
- Configure database schema for store configuration  
- Implement store registration and onboarding flow  

✅ **Deliverable:** Working app installation creating Teable instance per store.

---

### **Milestone 2: Data Import & Teable Setup (Weeks 3–4)**

**Week 3:**
- Implement Shopify Admin API integration  
- Create Teable table schemas (products, variants, collections)  
- Implement bulk import with progress tracking  
- Handle product image import  

**Week 4:**
- Add collections import  
- Implement validation and error handling  
- Create import summary and reporting  
- Test with large catalogs (1000+ products)  

✅ **Deliverable:** One-click import of entire Shopify catalog into Teable.

---

### **Milestone 3: Core Synchronization (Weeks 5–6)**

**Week 5:**
- Implement “Push to Shopify” manual sync  
- Add change detection logic in Teable  
- Handle variants and inventory updates  
- Build sync status indicators  

**Week 6:**
- Configure Shopify webhooks for product changes  
- Implement webhook verification  
- Update Teable data via webhooks  

✅ **Deliverable:** Reliable two-way synchronization.

---

### **Milestone 4: Audit & Rollback (Weeks 7–8)**

**Week 7:**
- Design audit log schema  
- Implement change tracking for all sync operations  
- Create audit log views in Teable UI  

**Week 8:**
- Implement rollback functionality  
- Create rollback UI and confirmation flow  
- Test rollback edge cases  

✅ **Deliverable:** Full audit trail and rollback capability.

---

### **Milestone 5: Polish & Integration (Weeks 9–10)**

**Week 9:**
- Implement comprehensive error handling  
- Add loading states and user feedback  
- Create sync conflict resolution  
- Add settings and configuration page  

**Week 10:**
- Performance optimization for large datasets  
- Implement rate limiting and API quota management  
- Create admin dashboard and monitoring tools  
- Conduct security audit  

✅ **Deliverable:** Production-ready, polished application.

---

### **Milestone 6: Testing & Deployment (Weeks 11–12)**

**Week 11:**
- End-to-end testing with multiple store scenarios  
- Load testing for large product catalogs  
- User Acceptance Testing (UAT)  
- Documentation completion  

**Week 12:**
- Production deployment  
- Prepare Shopify App Store submission  
- Create marketing materials and listing  
- Final security and compliance review  

✅ **Deliverable:** Live MVP available in Shopify App Store.

---

## 📊 Success Metrics

| **Milestone** | **Success Metric** |
|----------------|--------------------|
| 1 | Successful app installation creates Teable instance |
| 2 | 100% product import accuracy with images |
| 3 | < 5-minute sync time for 500 products |
| 4 | Complete audit trail for all changes |
| 5 | < 1% user error rate and polished UX |
| 6 | Successful Shopify App Store approval and first users onboarded |

---

## 🧰 Tech Stack Summary

| **Component** | **Technology** |
|----------------|----------------|
| **Frontend (Shopify App)** | Next.js + Shopify Polaris |
| **Backend Service** | Node.js / Express.js / Next.js API Routes |
| **Main App Database** | Neon PostgreSQL |
| **Per-Store Database** | Neon PostgreSQL Branches |
| **Core Product** | Teable (Self-hosted via Docker) |
| **Infrastructure as Code** | Terraform / Pulumi |
| **Hosting** | Provided frontend + backend servers |
| **Version Control** | GitHub |
| **Deployment** | Docker + Automated Provisioning |

---

## 🧾 License

This project is proprietary and intended for internal MVP development and Shopify App Store submission.

---

## ✨ Authors

**Project Lead:** Algobasket 
**Codename:** *TeableSync*  
**Mission:** Build a seamless bridge between Shopify and Teable to empower data-driven store management.

---

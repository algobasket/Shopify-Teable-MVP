# Shopify-Teable-MVP
Shopify-Teable-MVP 

Project Definition: "TeableSync"

Product Vision: A Shopify app that provides e-commerce owners with a powerful, spreadsheet-like interface (powered by Teable) for managing their entire catalog, featuring robust two-way synchronization, audit trails, and rollback capabilities.

Core Value Proposition: Combines the simplicity of a spreadsheet with the power of a database, directly synced with your Shopify store, eliminating the need for complex and expensive PIM systems for SMBs.

MVP Scope & Feature Prioritization

For the MVP, we focus on the core workflow from your table. "Nice-to-haves" can be developed post-MVP.

MVP Feature	                            Priority	  Description
1. Shopify App Installation & Auth	    Critical	  OAuth flow from Shopify App Store. Creates a unique Teable instance per store.
2. Initial Product & Collection Import	Critical	  One-click bulk import of all products, variants, images, and collections from Shopify into the store's dedicated Teable instance.
3. Two-Way Sync (Basic)	                Critical	  Teable -> Shopify: Manual "Push to Shopify" button. Shopify -> Teable: Near real-time sync via Shopify webhooks for changes made in the Shopify admin.
4. Teable UI for Data Management	      Critical	  The store owner interacts directly with the hosted Teable instance to view, filter, and edit their product data in a grid.
5. Basic Audit Log	                     High	      A separate table within the Teable instance logs all sync operations (who, what, when).
6. Simple Rollback	                     High	      Ability to revert a product to its state before the last sync, using the audit log.
7. Project Setup & DevOps Lite	        Critical	  Infrastructure-as-Code (e.g., Terraform) to automate the provisioning of a Teable instance + Neon DB for each new store.

Technical Architecture & Stack

Here is a proper, scalable architecture for the MVP.

<img width="789" height="306" alt="image" src="https://github.com/user-attachments/assets/0bad56a7-192b-4017-b6eb-fc640daa1ba8" />

1. Shopify App (Frontend)

Technology: Next.js (or Remix) for the initial setup and onboarding UI. This is the "glue" that the merchant interacts with inside the Shopify Admin.

Hosting: Your provided frontend servers.

2. TeableSync Backend Service

Technology: Next.js API Routes (if using Next.js) or a separate Express.js server.

Hosting: Your provided frontend servers (can run Node.js backends).

Responsibilities:

Handling Shopify OAuth.

Receiving and processing Shopify Webhooks.

Managing the lifecycle of Teable instances (creation, configuration).

Orchestrating syncs between Shopify and Teable.

3. Teable Instances (Core Product)

Technology: Teable, self-hosted.

Hosting: Your provided servers. Each store gets its own instance (for data isolation).

Database: Each Teable instance will connect to its own dedicated Neon.com PostgreSQL branch. This is key.

4. Main Application Database

Technology: Neon.com PostgreSQL

Purpose: Stores app-level data, not the product data.

Store configurations (shop_domain, access_token, teable_instance_url, teable_db_connection_string).

Sync job history and audit metadata.

User sessions.

Implementation Plan: What It Will Take
Phase 1: Foundation & Infrastructure (DevOps Lite)
This is the most critical phase to achieve your "low pain" goal.

Infrastructure as Code (IaC):

Use Terraform or Pulumi to define your entire infrastructure.

Script: A "create-store" script that, upon successful OAuth:

Creates a new PostgreSQL branch on Neon.com for the store.

Spins up a new Teable Docker container on your server, pointing to this new DB branch.

Records the teable_instance_url and teable_db_connection_string in the main app DB.

Benefit: This makes provisioning and destruction of store environments automatic and repeatable, minimizing manual DevOps.

Shopify App Setup:

Create the app in your Shopify Partner dashboard.

Implement OAuth flow in your Next.js/Express backend to get the access_token.

Phase 2: Core Data Synchronization
Import from Shopify (Step 3):

Backend service uses Shopify Admin API (GraphQL) to fetch products, collections, images, and metafields.

Service structures this data and uses the Teable API to insert it into the correct tables in the store's Teable instance.

Implement a progress bar by breaking the import into batches.

Data Mapping (Step 4):

For MVP, this can be implicit. Map core Shopify fields (title, description, price, SKU) to pre-defined Teable table columns.

Advanced mapping (for metafields) can be a post-MVP feature.

Sync: Teable -> Shopify (Step 7):

Create a "Push to Shopify" button in your app's UI.

When clicked, the backend fetches the updated data from the Teable API, compares it with the last known state (or uses a _dirty flag), and makes batch updates to Shopify via the Admin API.

Sync: Shopify -> Teable (Step 8):

Register Shopify Webhooks (products/update, products/create, products/delete) to point to your backend.

When a webhook is received, your backend forwards the change to the appropriate Teable instance via its API.

Phase 3: MVP Features & Polish
Audit Log (Step 9):

Create an "Audit Log" table in the Teable instance schema.

Every sync action (both directions) writes a record to this table with: timestamp, action, product_id, user, old_value, new_value.

This can be done by your backend service as it processes syncs.

Rollback (Step 9):

In the Teable UI, a custom view or a button in your app's UI can fetch the audit log for a product.

"Rollback" would take the old_value from a selected audit record and push it back to Teable (and subsequently to Shopify if desired).

Onboarding & UI (Step 2):

Build the guided setup flow in your Next.js app to help users connect their store and perform the first import.






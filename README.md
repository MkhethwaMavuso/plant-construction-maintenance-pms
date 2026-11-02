# plant-construction-maintenance-pms
A comprehensive low-code Project Management System (PMS) built on Adalo to automate plant construction/maintenance tracking, labor and equipment costing, quotation approvals, and purchase order generation.

🚀 Plant Construction & Maintenance PMS: 
📋 Table of Contents📌 1. Product Requirements Document (PRD)🗄️ 2. Database Schema & Entity Architecture (Adalo Collections)🏗️ 3. System Architecture & Adalo Data Flow🛡️ 4. Security & Access Control🔌 5. API Contract Specifications🚀 Setup & Configuration📌 1. Product Requirements Document (PRD)❓ The ProblemOperational Fragmentation: Plant construction and maintenance projects struggle with disconnected workflows across job site tracking, resource estimation, quotation generation, and purchase order sign-offs[cite: 4].Manual Costing Errors: Inconsistent profit margins and equipment markups during manual cost estimations lead to severe job profitability leakage[cite: 4].Approval Delays: Unstructured approval channels for high-value quotations and purchase orders slow down site progress and vendor sourcing[cite: 4].💡 The SolutionA centralized Project Management System (PMS) built on Adalo that enables Project Managers (PMs) to register jobs, record required labor and spare/equipment items with auto-computed markups, compile quotations, execute formal manager sign-offs, and automatically issue Purchase Orders upon quotation approval[cite: 4].🎯 Key Functional Requirements🔐 FR-1 [User Management & Role Scoping]: Secure user registration, authentication, and role-based interface gating (Project Manager, Department Manager, Admin)[cite: 4].🏗️ FR-2 [CRUD Projects & Job Site Tracking]: Capture full project details, including auto-generated project numbers, location, start/end dates, job status, and job comments[cite: 4].🧮 FR-3 [CRUD Quotations & Resource Costing]: PMs log required labor (hours, hourly cost, profit margin) and equipment/spares (unit cost, quantity, markup), which the system automatically aggregates into total quotation prices[cite: 4].✅ FR-4 [Approve/Reject Quotations Workflow]: Managers review compiled project quotations with single-click Approve or Reject actions[cite: 4].📄 FR-5 [Automated Purchase Order Generation]: Upon quotation approval, the system automatically creates and links a formal Purchase Order for supplier dispatch[cite: 4].🗄️ 2. Database Schema & Entity Architecture (Adalo Collections)The backend runs on Adalo's Relational Database Collections, establishing clear foreign-key linkages across users, project lifecycles, financial cost structures, and purchase orders[cite: 4].📊 Entity-Relationship OverviewCollection NamePrimary Key / IdentifierRelational LinksKey Attributes & Description👤 UsersIDProjects, QuotationsEmail, Password, Full Name, User Type[cite: 4]🏗️ ProjectsIDUsers (PM ID), Quotations, Purchase Orders, HandoverProject Number, Title, Description, Location, Start Date, End Date, Job Comments, Job Status[cite: 4]📋 Project TypesIDProjectsDescription[cite: 4]🧮 QuotationsIDProjects (Project ID), Labor, SparesQuotation Number, Title, Description, Date Created, Status ('Pending', 'Approved', 'Rejected')[cite: 4]👷 LaborIDQuotations (Quotation ID)Description, Cost Per Hour, Hours, Labor Cost, Profit Margin, Labor SP[cite: 4]🔧 Spares / EquipmentIDQuotations (Quotation ID)Description, Unit Cost, Quantity, Cost Price, Mark-up, Selling Price[cite: 4]📦 Purchase OrdersIDProjects (Project ID), Quotations (Quotation ID)Purchase Order Number, Title, Description, Date, Status[cite: 4]📑 HandoverIDProjects (Project ID)Handover Note Number, Title, Description, Date Created[cite: 4]🏗️ 3. System Architecture & Adalo Data FlowThe architecture follows a multi-tier structure separating client-side presentation, automated formula logic, and relational persistence[cite: 4].┌──────────────────────────────────────────────────────────────────┐
│                    ADALO FRONTEND CLIENT APP                     │
│                                                                  │
│  ┌───────────────────────┐             ┌───────────────────────┐ │
│  │ Project & Resource    │             │ Manager Approval      │ │
│  │ Form Inputs           │             │ Dashboard             │ │
│  └───────────┬───────────┘             └───────────────────────┘ │
└──────────────│───────────────────────────────────────────────────┘
               │
               │ 1. Capture Labor & Equipment Requirements
               ▼
┌──────────────────────────────────────────────────────────────────┐
│              APPLICATION LOGIC & CALCULATION LAYER               │
│                                                                  │
│  • Labor SP = (Cost Per Hour * Hours) * (1 + Profit Margin)      │
│  • Spares Selling Price = (Unit Cost * Quantity) * (1 + Markup)  │
│  • Total Quotation Sum = Sum(Labor SP) + Sum(Spares SP)          │
└──────────────┬───────────────────────────────────────────────────┘
               │
               │ 2. Submit Quotation for Review
               ▼
┌──────────────────────────────────────────────────────────────────┐
│             WORKFLOW ENGINE (Quotation State Transition)        │
│                                                                  │
│  • IF Approved: Auto-generate Purchase Order linked to Project   │
│  • IF Rejected: Return Quotation for PM Revision                 │
└──────────────┬───────────────────────────────────────────────────┘
               │
               │ 3. Persist Purchase Order & Updated Status
               ▼
┌──────────────────────────────────────────────────────────────────┐
│                   ADALO BACKEND DATABASE                         │
│                                                                  │
│  • Relational Collections: Projects, Quotations, POs, Handovers  │
└──────────────────────────────────────────────────────────────────┘
🔄 Data Execution Lifecycle🏗️ Project Setup: The Project Manager creates a new project and attaches labor and equipment requirements[cite: 4].🧮 Quotation Generation: The system auto-calculates selling prices with applied profit margins/markups and creates a linked Quotation record[cite: 4].👔 Manager Approval: A Department Manager reviews the quotation and selects Approve or Reject[cite: 4].⚡ PO Generation: Upon approval, an Adalo action automatically creates a linked Purchase Order with an assigned PO number[cite: 4].🚚 Execution: The PO status updates to 'Issued', allowing procurement staff to source materials and proceed with job site execution[cite: 4].🛡️ 4. Security & Access Control🔐 Access Control MatrixRole / User TypeCreate / Edit ProjectsLog Labor & EquipmentApprove / Reject QuotationsIssue Purchase Orders👷 Project Manager (PM)✅✅❌❌👔 Department Manager✅✅✅✅⚙️ System Administrator✅✅✅✅🛡️ Component Visibility & Action GuardsAdalo screen components enforce strict role and state conditions:Approve / Reject Action Buttons: Visible ONLY if Logged In User > User Type == "Manager" AND Current Quotation > Status == "Pending".Create Purchase Order Action: Automatically executed via trigger actions only when Current Quotation > Status == "Approved".Locking Approved POs: Purchase orders linked to approved quotations are read-only to preserve financial auditability[cite: 4].🔌 5. API Contract Specifications📥 5.1 Project Creation API (createProject)Type: Internal Collection MutationAccess Level: Project Managers & AdminsInput Payload:JSON{
  "title": "Plant Delta Boiler Overhaul",
  "description": "Scheduled maintenance and equipment replacement",
  "location": "Sector 4 Plant",
  "start_date": "2026-11-10",
  "end_date": "2026-12-01",
  "project_type_id": "proj_type_maintenance"
}
Execution Logic: Auto-generates Project Number (e.g., PRJ-10024), sets Job Status = 'Planning', and logs creation timestamp[cite: 4].📤 5.2 Quotation Calculation API (createQuotation)Type: Internal Collection Mutation & Formula ProcessingAccess Level: Project ManagersInput Payload:JSON{
  "project_id": "prj_10024",
  "title": "Quotation for Boiler Overhaul",
  "labor_cost_per_hour": 45.00,
  "labor_hours": 120,
  "labor_profit_margin": 0.20,
  "spares_unit_cost": 1500.00,
  "spares_quantity": 4,
  "spares_markup": 0.15
}
Execution Logic: Computes total labor selling price ($6,480.00) and total spares selling price ($6,900.00), aggregates the quotation sum ($13,380.00), and sets Status = 'Pending'.⚡ 5.3 Quotation Approval & PO Trigger (approveQuotation)Type: State Transition & Action CascadeAccess Level: Department ManagersExecution Logic:Plaintext1. UPDATE Quotation SET Status = 'Approved' WHERE id = Current_Quotation.id
2. CREATE Purchase_Order (
     purchase_order_number = "PO-" + Quotation.Quotation_Number,
     project_id = Current_Quotation.project_id,
     status = "Issued",
     date = Current_Timestamp
   )
🚀 Setup & ConfigurationAdalo Database Setup:Create the required Adalo Collections (Users, Projects, Project Types, Quotations, Labor, Spares, Purchase Orders, Handover)[cite: 4].Formula Setup:Configure custom math formulas for Labor SP (Cost * Hours * (1 + Margin)) and Spares SP (Unit Cost * Qty * (1 + Markup)) inside Adalo form actions[cite: 4].Approval Cascades:Attach conditional multi-step actions on the Manager Approval screen to automatically generate Purchase Orders upon quotation approval.

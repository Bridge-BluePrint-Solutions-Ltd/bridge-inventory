# Bridge Inventory User Guide

**Audience:** Padmore (Bridge BluePrint Solutions Ltd)  
**System:** Bridge Inventory — Snipe-IT (v8.x) for office asset tracking  
**Local app:** http://localhost:8000  
**Related docs:** [LOCAL-SETUP.md](LOCAL-SETUP.md) · [BRIDGE-INVENTORY-PLAN.md](BRIDGE-INVENTORY-PLAN.md)

This guide explains how to use Snipe-IT for **Bridge office inventory**: printers, laptops, gadgets, furniture, cables, chairs, desks, monitors, and similar items. It is a practical how-to — what each concept means, when to use it, how to create records, how tagging and checkout work, and which settings to configure first.

Official reference: [Snipe-IT documentation](https://snipe-it.readme.io/docs).

---

## 1. Introduction — what this is (and is not)

### What Bridge Inventory is for

Bridge Inventory is your **office asset register**. Use it to answer questions like:

- Which laptop does Ama have, and when was it issued?
- How many spare HDMI cables are in the Accra office store cupboard?
- Is the HP LaserJet on the 2nd floor still under warranty?
- Which chairs and desks belong to which meeting room?
- Are we low on A4 paper or toner?

Every unique, valuable, or trackable item can get an **asset tag**. Bulk or depleting stock (mice, paper, toner) can be tracked by **quantity**. Software seats can be tracked as **licenses** if you need them later.

### What it is NOT

| Not this | Why |
|----------|-----|
| Manufacturing MRP / ERP | No bill-of-materials production planning, shop-floor routing, or purchase-order MRP. |
| Accounting ledger | Depreciation reports help IT/finance *estimate* value; they do not replace your books. |
| Full CMDB / network discovery | You can sync tools later (Jamf, Intune, etc.); day one is manual or CSV import. |
| Warehouse WMS | Locations and quantities exist, but this is not a pick/pack warehouse system. |

Think **IT + office inventory**, not factory planning.

---

## 2. Mental model — which item type to use

Snipe-IT has five main item types. Choosing the right one keeps reports and checkout workflows sane.

| Type | Meaning | Unique tag? | Checkout to | Check back in? | Bridge examples |
|------|---------|-------------|-------------|----------------|-----------------|
| **Asset** | One specific physical item that matters individually | Yes (required, unique) | User (preferred), location, or another asset | Yes | Laptop, monitor, printer, phone, desk, chair (if tagged), projector |
| **Accessory** | Similar items tracked by quantity; individual serial usually does not matter | No (quantity on hand) | User or asset | Yes (returns to pool) | Mouse, keyboard, HDMI cable, USB hub, webcam, headset |
| **Consumable** | Stock that is used up | No (quantity depletes) | User only | **No** | A4 paper, toner/ink, batteries, cleaning wipes |
| **Component** | Part installed *into* an asset | No (qty; assigned to assets) | **Asset** only | Yes (can move between assets) | RAM stick, SSD, laptop battery, docking-station module |
| **License** | Software seats / keys | Seats, not asset tags | User or asset | Yes (seat freed) | Microsoft 365, Adobe, antivirus seats (optional for Bridge) |

### Decision table for common Bridge office items

| Item | Use as… | Why |
|------|---------|-----|
| MacBook / Windows laptop | **Asset** | Unique serial; assigned to one person |
| External monitor | **Asset** | Unique; often stays with a person or desk |
| Office printer / MFP | **Asset** | Unique; location matters; warranty/maintenance |
| Desk / chair (tagged) | **Asset** | If you care which exact unit is where |
| Generic spare chair (untagged bulk) | **Accessory** | Quantity of “conference chairs” is enough |
| Mouse / keyboard / headset | **Accessory** | Interchangeable; track qty + who has one |
| HDMI / USB-C / Ethernet cable | **Accessory** | Bulk stock; optional checkout to person or room asset |
| A4 paper reams | **Consumable** | Depletes; checkout reduces stock |
| Toner / ink cartridges | **Consumable** | Depletes when issued |
| Extra RAM / SSD upgrades | **Component** | Installed into a specific laptop asset |
| Microsoft 365 seats | **License** | Seat count + who/which machine uses a seat |
| Whiteboard markers | **Consumable** | Used up |
| Portable projector | **Asset** | Unique, often checked out for meetings |

**Rule of thumb:** If you would put a sticker with a unique ID on it and care *which* one it is → **Asset**. If you only care *how many* you have → **Accessory** or **Consumable**. If it lives *inside* another device → **Component**.

---

## 3. Setup order — configure these before bulk data entry

Do **Admin / Settings foundation work first**. Creating assets before categories, models, and status labels leads to messy rework.

Recommended order:

1. **Company / branding** — site name, logo, default currency, timezone (Bridge: `Africa/Accra`).
2. **Categories** — broad types (Laptops, Monitors, Furniture, …). Categories also control EULA/acceptance and checkin/checkout email options.
3. **Manufacturers** — Apple, Dell, HP, IKEA, etc.
4. **Asset Models** — specific make/model (e.g. “MacBook Pro 14 M3”, “HP LaserJet Pro M404”). Every asset needs a model.
5. **Status Labels** — Ready to Deploy, Deployed-related deployable statuses, Pending (imaging/repair), Archived (lost/broken).
6. **Locations** (and departments if useful) — Accra HQ, Store cupboard, Meeting Room A, Remote/Home.
7. **Suppliers** — who you buy from (optional but useful for purchase history).
8. **Custom fields / fieldsets** — only if default fields are not enough (e.g. SIM, phone number).
9. **Labels / barcodes** — asset tag format, QR vs 1D barcode, label size for your printer.
10. **Users** — staff who will receive checkouts (or import them).
11. **Then** create assets, accessories, consumables, and components.

Also set early under **Admin → Settings**:

- Email domain / username format (helps CSV import invent emails/usernames).
- Auto-incrementing asset tags (or a Bridge prefix scheme — see §4 Labels).
- Alert email address for low stock / warranty (can use Mailpit locally).

---

## 4. Deep dive — major areas with Bridge examples

### 4.1 Assets

**What:** Unique tagged items. Each asset **must** have a unique **asset tag**.

**When to use:** Laptops, monitors, printers, phones, projectors, tagged furniture, any item you might audit by walking around with a scanner.

**How to create (UI):**

1. Ensure Category → Manufacturer → Asset Model exist.
2. Go to **Assets → Create New**.
3. Pick **model**, **status** (e.g. Ready to Deploy), **location**.
4. Enter or accept **asset tag** and **serial number**.
5. Optional: purchase date, cost, warranty, supplier, notes, photo.
6. Save. Later: **Checkout** to a user.

**Checkout / checkin:**

- **Checkout** marks the asset as in someone’s possession (preferred: a **user**, not only a location). That prevents double-booking.
- **Checkin** returns it to inventory (or to repair). Choose an appropriate **status** (Ready to Deploy, Pending Repair, etc.).
- Official guidance: prefer checkout to **people**; locations and other assets cannot be held responsible for loss or damage.

**Bridge tip:** Checkout “BRIDGE-LT-0042” (MacBook) to employee “Kojo Mensah”. If Kojo leaves, check the laptop back in, set status to Pending Wipe / Ready to Deploy, then checkout to the next person.

---

### 4.2 Asset Models + Categories + Manufacturers

These three form a hierarchy:

```
Manufacturer (Apple)
  └── Asset Model (MacBook Pro 14" M3)  ← belongs to a Category (Laptops)
        └── Assets (BRIDGE-LT-0001, BRIDGE-LT-0002, …)
```

| Concept | Role | Bridge examples |
|---------|------|-----------------|
| **Category** | Bucket + policy (EULA, emails, requestable) | Laptops, Desktops, Monitors, Printers, Furniture, Networking, Phones/Tablets |
| **Manufacturer** | Brand | Apple, Dell, HP, Lenovo, Samsung, IKEA, Local fabricator |
| **Asset Model** | Specific product line; inherits EOL, depreciation, fieldset, default image | Dell Latitude 5540, HP LaserJet Pro M404dn, “Bridge Standing Desk 140cm” |

**Why models matter:** Attributes like depreciation, EOL months, MAC address field, requestable flag, and **custom fieldsets** live on the model and apply to every asset of that model. Upload a model photo once instead of per laptop.

Create models **before** mass-creating assets.

---

### 4.3 Status Labels

Every asset has a **status label**. Each label belongs to one of four **types**:

| Type | Meaning | Can assign (checkout)? | Bridge examples |
|------|---------|------------------------|-----------------|
| **Deployable** | Available or currently assigned | Yes | Ready to Deploy |
| **Pending** | Not ready yet | No | Awaiting Imaging, Out for Repair, Ordered |
| **Undeployable** | Cannot be assigned | No | Reserved (internal hold) — use sparingly |
| **Archived** | Hidden from normal lists (unless settings say otherwise) | No | Lost/Stolen, Broken beyond repair, Sold/Disposed |

When a **Deployable** asset is checked out to a user, Snipe-IT treats it as **Deployed** (meta status).

**Suggested Bridge starter set:**

| Label | Type |
|-------|------|
| Ready to Deploy | Deployable |
| Deployed (optional custom; often implicit) | Deployable |
| Awaiting Setup / Imaging | Pending |
| Out for Repair | Pending |
| Lost / Stolen | Archived |
| Retired / Disposed | Archived |

Good status labels make the dashboard and reports useful at a glance.

---

### 4.4 Locations

Locations answer **where** something sits or is stored.

**Bridge starter locations (example):**

| Location | Use |
|----------|-----|
| Accra HQ | Default office |
| Accra HQ — Store / IT cupboard | Spares and bulk accessories |
| Meeting Room A / B | Room-assigned printers, TVs, furniture |
| Reception | Shared devices |
| Remote / Work from home | Assets checked out to remote staff (still prefer user checkout; location as home base) |

You can nest or relate locations (parent/child) depending on how detailed you want maps of the office. Color tags on locations are optional visual cues in lists.

**Departments** (if enabled in your workflow) group people (Engineering, Ops, Admin) separately from physical places — useful for reporting, not a substitute for locations.

---

### 4.5 Accessories

**What:** Quantity-tracked items that are not special enough for individual asset tags.

**Checkout:** To a **user** or an **asset**. Checking in returns quantity to the pool.

**Bridge examples:** Logitech mice (qty 20), USB-C hubs (qty 8), HDMI cables (qty 15), spare keyboards.

Set a **minimum quantity** so low-stock alerts fire when you approach the floor.

**When not to use:** If each unit has a serial you care about (e.g. expensive docking stations) → make them **Assets** instead.

---

### 4.6 Consumables

**What:** Stock that is **consumed**. Checkout reduces quantity permanently for that issue; there is **no checkin**.

**Checkout:** To **people** only.

**Bridge examples:** A4 paper (reams), toner for the office MFP, AA batteries, scrubbing wipes.

Track **min qty** for reorder alerts. Issue “2 reams to Facilities” when someone restocks the printer area — quantity drops accordingly.

---

### 4.7 Components

**What:** Parts that are installed **into assets** (not handed to people as the primary assignment).

**Checkout:** To an **asset** only (e.g. 16GB RAM → MacBook BRIDGE-LT-0007).

**Bridge examples:** RAM upgrades, replacement SSDs, spare laptop batteries kept as stock then installed.

When a laptop is retired, check components back in if they will be reused in another machine.

---

### 4.8 Licenses (optional for Bridge)

**What:** Software entitlements with a seat count (and optional license keys).

**Checkout:** Seat to a **user** or an **asset**.

Use this when Bridge needs to prove who is using Microsoft 365, Adobe, antivirus, or similar. Skip on day one if software is managed only in the vendor admin consoles — you can add licenses later without changing asset data.

Set expiration dates and alert thresholds so renewals are not a surprise.

---

### 4.9 Users & checkout / checkin

**Users** are people (employees, contractors) who can receive assets, accessories, consumables, and license seats.

**Typical Bridge laptop lifecycle:**

1. Asset created with status **Ready to Deploy**, location Accra HQ — Store.
2. IT images the machine → status **Awaiting Setup** (Pending) while working, then back to **Ready to Deploy**.
3. **Checkout** to the employee; category may email an acceptance / EULA if enabled.
4. Employee leaves or swaps machine → **Checkin**, note condition, set status (Ready to Deploy or Out for Repair).
5. Optionally run an **audit** later to confirm the tag still matches reality.

Create users manually or **import** them (CSV). Managers can view direct reports’ assigned items if **Manager View** is enabled in General Settings.

---

### 4.10 Maintenance & depreciation (brief)

**Maintenance:** Log repairs, upgrades, and service visits against an asset (date, cost, supplier, notes). Useful for printers and aging laptops (“replaced fuser 2026-03”).

**Depreciation:** Optional financial estimate over time. Configure depreciation methods under Settings (linear / half-year conventions), attach a depreciation schedule to **asset models**, and enter purchase **cost** + date on assets. Use reports for ballpark book value — coordinate with finance; this is not a full accounting system.

For Bridge day one, you can skip depreciation and add it when finance asks for an IT asset valuation.

---

### 4.11 Labels, barcodes, and asset tags

**Asset tag** = unique ID for each asset (e.g. `BRIDGE-LT-0001`). Often printed on a sticker.

**How tagging works in Snipe-IT:**

1. Choose a tag scheme under Admin (auto-increment and/or prefix). Example Bridge scheme:
   - `BRIDGE-LT-####` laptops  
   - `BRIDGE-MN-####` monitors  
   - `BRIDGE-PR-####` printers  
   - `BRIDGE-FN-####` furniture  
   Or use simple auto-increment `BRIDGE-0001` if you prefer one sequence.
2. Enable barcodes in **Admin → Settings**.
3. Configure **Labels** (size, 1D type, QR on/off) to match your label printer stock.
4. From an asset list, select assets → **Generate Labels**.
5. Stick labels on devices. QR codes open the asset page on a phone; 1D barcodes work with USB/Bluetooth scanners in the asset search box.

**Barcode types:** Prefer **C128** for alphanumeric tags like `BRIDGE-LT-0001`. Strict numeric formats (EAN/UPC) will fail if tags contain letters — that is a barcode spec limit, not a Snipe-IT bug.

If you change `APP_URL`, regenerate barcodes (clear cached barcode images) so QR links stay correct. See official [Barcodes](https://snipe-it.readme.io/docs/barcodes) and [Asset Labels](https://snipe-it.readme.io/docs/asset-labels) docs.

---

### 4.12 Importing CSV (high level)

Use **Import** in the web UI for bulk load. Always **Admin → Backups** first.

| Tip | Detail |
|-----|--------|
| Format | Comma-separated CSV; header row; no trailing blank lines or stray spaces in headers |
| Mapping | Importer guesses columns; override in the UI if wrong |
| Order | Import manufacturers/categories/locations/users before assets when possible |
| Quality | `Dell Inspiron` ≠ `Dell Insprion` — duplicates appear if spelling drifts |
| Checkout | GUI import can check out to people or locations; not to other assets |
| Samples | See `sample_csvs/` in this repo and official import docs |
| Large files | Split big CSVs; CLI importer exists for heavy loads |

Import types include assets, models, users, accessories, consumables, components, licenses, locations, categories, manufacturers, suppliers.

---

### 4.13 Settings worth knowing

| Area | What to configure for Bridge |
|------|------------------------------|
| **General** | Company name, branding, email/username formats, EULA defaults, unique serials (on if you want serial uniqueness enforced), dashboard message, checkin notes required (optional) |
| **Security** | Password length/complexity; enable **2FA** for admins; SAML/SSO later if needed |
| **Notifications** | Alert email(s), low inventory threshold, warranty/license expiry days, audit warning interval |
| **Labels / Barcodes** | Tag prefix, QR + C128, label dimensions for your printer |
| **Backups** | Use **Admin → Backups** regularly; Docker volumes matter in prod. Local Mailpit does not replace backups |
| **Depreciations** | Only when finance wants numbers |

Local Docker already uses Mailpit for mail testing (`http://localhost:8025`). Production will use Resend (or similar) later — see the living plan.

---

## 5. Suggested Bridge starter taxonomy

Use this as a concrete day-one catalog. Adjust names to taste; consistency matters more than perfection.

### Categories (assets unless noted)

| Category | Typical type |
|----------|--------------|
| Laptops | Asset |
| Desktops / Workstations | Asset |
| Monitors | Asset |
| Printers / Scanners | Asset |
| Phones / Tablets | Asset |
| Networking (routers, APs, switches) | Asset |
| AV / Meeting (projectors, conference cams) | Asset |
| Furniture | Asset |
| Peripherals | Accessory category |
| Cables & Adapters | Accessory category |
| Print supplies | Consumable category |
| Office supplies | Consumable category |
| Computer parts | Component category |
| Software | License category |

### Example manufacturers

Apple, Dell, HP, Lenovo, Samsung, Logitech, Cisco/Ubiquiti (as applicable), IKEA / local furniture supplier, “Bridge” (for custom-built desks or unlabeled kits).

### Example asset models

| Model | Category | Notes |
|-------|----------|-------|
| MacBook Pro 14 M3 | Laptops | Fieldset optional: Warranty portal URL |
| Dell Latitude 5540 | Laptops | |
| Dell UltraSharp 27 | Monitors | |
| HP LaserJet Pro M404dn | Printers | |
| Bridge Staff Desk 140cm | Furniture | |
| Bridge Ergonomic Chair | Furniture | Tag if individually tracked |
| Epson EB-X series projector | AV / Meeting | |

### Example accessories / consumables / components

| Name | Type | Min qty idea |
|------|------|--------------|
| Logitech M185 mouse | Accessory | 5 |
| USB-C hub 7-in-1 | Accessory | 3 |
| HDMI 2m cable | Accessory | 10 |
| A4 paper (ream) | Consumable | 20 |
| HP 58A toner | Consumable | 2 |
| DDR4 16GB SODIMM | Component | 4 |
| 1TB NVMe SSD | Component | 2 |

### Status labels

Ready to Deploy · Awaiting Setup · Out for Repair · Lost/Stolen · Retired/Disposed (see §4.3).

### Locations

Accra HQ · Accra HQ — IT Store · Meeting Room A · Meeting Room B · Reception · Remote.

---

## 6. Day-1 checklist — populate the empty system

Work top to bottom after the web setup wizard creates your admin user.

- [ ] **Branding** — company name “Bridge BluePrint Solutions Ltd” / “Bridge Inventory”, logo, timezone `Africa/Accra`, currency.
- [ ] **General settings** — email domain, username format, alert address (your email; verify in Mailpit locally).
- [ ] **Security** — strong admin password; enable 2FA when ready.
- [ ] **Status labels** — create the Bridge set above; retire unused defaults or rename them.
- [ ] **Locations** — Accra HQ + store + key rooms.
- [ ] **Categories** — Laptops, Monitors, Printers, Furniture, Peripherals, Cables, Print supplies, etc.
- [ ] **Manufacturers** — Apple, Dell, HP, Logitech, …
- [ ] **Asset models** — at least one model per category you will use this week.
- [ ] **Label settings** — prefix + C128/QR; print a test label on scrap stock.
- [ ] **Users** — add yourself and a few staff (or CSV import).
- [ ] **Seed assets** — 5–10 real items (one laptop, one monitor, one printer, one chair/desk) end-to-end including a checkout.
- [ ] **Seed accessories / consumables** — mice, cables, paper with quantities and min levels.
- [ ] **Backup** — Admin → Backups after the first good import.
- [ ] **Optional** — custom fieldset for phones (IMEI, number); licenses; depreciation.

Only after the taxonomy feels right should you CSV-import the full office list.

---

## 7. Other useful features

| Feature | Why Bridge might care |
|---------|------------------------|
| **Audit** | Periodically confirm assets still exist where the system says; set next audit dates and warnings |
| **Reports** | Activity, audit report, depreciation, expiring warranties/licenses, low inventory, expected checkin |
| **Requestable assets / models** | Staff can request a spare laptop or meeting projector from the UI if you mark items requestable |
| **Kits** | Bundle items for onboarding (laptop model + mouse + bag) if your Snipe-IT version exposes predefined kits |
| **API** | Automate create/checkout from scripts; see [API overview](https://snipe-it.readme.io/reference/api-overview) |
| **Sync adapters** | Later: Jamf / Intune / etc. for laptop truth — optional |
| **Multi-company (ish)** | Only if Bridge ever isolates multiple legal entities in one install |
| **User inventory email** | Remind users what is assigned to them |

You do not need all of these on day one. Master assets + checkout + labels first.

---

## 8. Links to official docs

| Topic | URL |
|-------|-----|
| Docs index (llms.txt) | https://snipe-it.readme.io/llms.txt |
| Overview / concepts | https://snipe-it.readme.io/docs/overview |
| Introduction | https://snipe-it.readme.io/docs/introduction |
| Asset models | https://snipe-it.readme.io/docs/asset-models |
| Custom fields | https://snipe-it.readme.io/docs/custom-fields |
| General settings | https://snipe-it.readme.io/docs/general-settings |
| Security | https://snipe-it.readme.io/docs/security-1 |
| Notifications | https://snipe-it.readme.io/docs/notifications |
| Barcodes | https://snipe-it.readme.io/docs/barcodes |
| Asset labels | https://snipe-it.readme.io/docs/asset-labels |
| Importing | https://snipe-it.readme.io/docs/importing |
| Importing assets | https://snipe-it.readme.io/docs/importing-assets |
| Backups | https://snipe-it.readme.io/docs/backups |
| Alerts & backups (scheduler) | https://snipe-it.readme.io/docs/configuring-alerts-backups |
| Depreciation types | https://snipe-it.readme.io/docs/depreciation-types |
| API overview | https://snipe-it.readme.io/reference/api-overview |
| Product feature list | https://snipeitapp.com/product |
| Live demo (upstream) | https://snipeitapp.com/demo/ |

Append `.md` to many ReadMe.io doc URLs for a markdown version (as noted in llms.txt).

---

## Quick reference card

| I have… | Create… |
|---------|---------|
| A specific laptop / printer / tagged chair | **Asset** (+ model/category) |
| A box of identical mice | **Accessory** with quantity |
| Toner or paper | **Consumable** |
| RAM going into a laptop | **Component** → checkout to that **asset** |
| Office 365 seats | **License** |
| Someone taking a laptop home | **Checkout** asset → **user** |
| Someone returning a laptop | **Checkin** + update **status** |
| Sticker for the device | Asset **tag** + **Generate Labels** |

---

*Maintained for Bridge BluePrint Solutions Ltd. Upstream product is [Snipe-IT](https://github.com/grokability/snipe-it) (AGPL-3.0) by Grokability. Last updated: 2026-09-28 (Africa/Accra).*

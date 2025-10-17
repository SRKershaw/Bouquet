# Use Cases.md - High-Level Use Cases for Retirement Portfolio Management System

## Introduction
This document specifies high-level use cases derived from the Requirements Summary in HL Requirements.md. Use cases focus on nominal (happy) paths with key alternatives for robustness, using UML activity notation for complex flows. They are grouped by subsection (e.g., Management) for traceability. Full coverage ensures extensible class definitions (Step 3). Placeholders for incomplete subsections. Initiation Pages list core entry points (evolvable during prototyping).

## Management Use Cases
Derived from Requirements Summary - Management (items 1–10). Focus: Core data entry and setup for extensible classes (e.g., Profile, Transaction). Properties as personal (not SIPP-held); valuations via adjustments. Pension generalised to "Pension Account" with sub-types: Uncrystallised (pre-drawdown), Crystallised Drawdown (flexi-access)—UFPLS as transaction flag, not sub-account. Bucket reassignments manual but prompted by transactions. Guided prompts for subtypes in onboarding. Consolidated pages (e.g., Accounts includes Institutions/Properties/Catalogue access). Tier selectable in Settings (Basic/Premium/Advanced default). New "Deposit" and "Interest" Income categories; Tax debit for manual deduction.

### UC-MGT-01: Register User and Create Profile
**Description:** User registers and creates a Profile with up to two Owners (typically partners, one as primary User).  
**Preconditions:** None.  
**Postconditions:** Profile created with at least one Owner; extensible for joint access.  
**Nominal Path:**  
1. User enters email/password and personal details.  
2. System validates and creates Profile with User as primary Owner.  
3. System saves Profile.  

**Initiation Pages:** Dashboard (initial signup), Settings (post-registration edit).  

**Alternatives:**  
- Add second Owner during creation: Enter partner details; system associates both to Profile.  
- Add second Owner post-registration: From Profile settings, select "Add Partner"; enter details and confirm shared visibility; system updates Profile.  

**UML Activity (Post-Registration Addition):**  
[Start] → [Select Add Partner] → [Enter Details] → [Decision: Valid?] → [Yes: Update Profile] → [End] / [No: Error & Retry]  

### UC-MGT-02: Create Institution
**Description:** User creates an Institution (e.g., bank/brokerage) as a provider for Accounts (prerequisite for UC-MGT-04).  
**Preconditions:** Profile exists.  
**Postconditions:** Institution added to system registry, linkable to Accounts.  
**Nominal Path:**  
1. User enters Institution details (e.g., name, type: Bank/Brokerage).  
2. System validates (e.g., uniqueness).  
3. System saves Institution.  

**Initiation Pages:** Accounts (inline during account creation).  

**Alternatives:**  
Select from curated list: Search/filter known providers (e.g., Hargreaves Lansdown); system auto-creates if not found.  

**UML Activity (Creation Flow):**  
[Start] → [Enter/Select Details] → [Decision: Unique/Valid?] → [Yes: Save] → [End] / [No: Alert & Retry]  

### UC-MGT-03: Add Assets to Catalogue
**Description:** User adds Assets (e.g., securities like stocks, ETFs) to the Catalogue via ticker/ID for later brokerage transactions (precondition for buys).  
**Preconditions:** Profile exists.  
**Postconditions:** Asset added to Catalogue with validation/enrichment.  
**Nominal Path:**  
1. User enters ticker/ID (e.g., VUSA.L for ETF).  
2. System validates (stubbed external API, e.g., yfinance) and enriches (name, sector—freeform text override).  
3. System saves to Catalogue (non-transactional, editable if unused).  

**Initiation Pages:** Securities, Accounts (inline for brokerage).  

**Alternatives:**  
Edit: Update unused Asset (e.g., correct name); non-deletable post-transaction for traceability.  
Bulk (Deferred): CSV import for multiple (single-asset for MVP).  

### UC-MGT-04: Create Accounts
**Description:** User creates Accounts at Institutions (e.g., Bank/Pension), owned by one or both Owners; sub-types for Bank (Current/Deposit), Brokerage (Trading/ISA/Pension: Uncrystallised/Crystallised Drawdown). Guided prompts for selection; overlaps with Institutions/Properties/Catalogue.  
**Preconditions:** Profile and Institution exist (via UC-MGT-02).  
**Postconditions:** Account added to Profile with ownership and sub-type recorded.  
**Nominal Path:**  
1. User selects existing or creates new Institution (invoke UC-MGT-02 if needed).  
2. User selects Account type (e.g., Brokerage) and sub-type (guided prompt: "Trading for general or ISA for tax-free?").  
3. User enters details (e.g., account number).  
4. System assigns ownership (default: primary Owner).  
5. System saves Account linked to Institution.  

**Initiation Pages:** Accounts (primary), Dashboard (quick add).  

**Alternatives:**  
Joint ownership: Select both Owners during step 4; system records shared flag.  
Inline Overlaps: On Accounts page, create Institution/Properties/Catalogue access (e.g., add security from Catalogue to new brokerage).  
Transaction Creation: From Accounts page, select account → add transaction (e.g., buy from Catalogue security).  

### UC-MGT-05: Input Transactions (Expanded for Comprehensive Types)
**Description**: User inputs Transactions with double-entry, supporting all types (credits/debits, buys/sells, transfers, fees, adjustments); categorized for income/budgets ("Deposit", "Interest" for Income; Tax for debits with manual deduction). Multi-currency/FX, UK-specific (Pension/CGT). Extensible to APIs. Transactions prompt bucket reassignments; pension-specific flags for UFPLS/crystallisation. MPAA/LSA totals queried from history. Source/destination distinguished per type.  
**Preconditions:** Profile and relevant Account exist; Assets in Catalogue for buys.  
**Postconditions:** Balances updated; versioned log created; projections/alerts triggered; prompted bucket reassignment if applicable; tracker totals updated via query.  
**Nominal Path (Generic):**  
1. User selects Account, type (credit/debit/buy/sell/transfer/fee/adjustment).  
2. User enters details (amount, currency/FX if multi, category, fees/tax; source/destination per type).  
3. System applies double-entry (e.g., debit source, credit destination; CGT pooling for sells).  
4. System validates (e.g., MPAA for Pension draws), versions, and saves.  
5. If liquidation (e.g., Bucket 2 sell), system prompts reassignment (invoke UC-MGT-07).  

**Initiation Pages:** Catalogue, Accounts, Portfolio, Transactions.  

**Sub-Use Cases (Types):**  
- **Credit (Inflow)**: Align with income categories (e.g., "Deposit" for initial cash, "Interest" for cash yields, State Pension—regular, auto-inflation; inheritance—ad-hoc). Nominal: Enter amount/category; credit Destination Account (no source—external). Alternative: Pension drawdown (flag "UFPLS Withdrawal" or "Crystallised Withdrawal", specify liquidated units; 25% tax-free if applicable).  
- **Debit (Outflow)**: Align with budget categories (e.g., Everyday—recurrent; Tax—manual amount deducted from Source Account cash—no destination—external). Nominal: Enter amount/category; debit Source Account. Alternative: Split (e.g., partial to Emergency).  
- **Buy/Sell Securities**: With unit price/quantity, fees (fixed/%); CGT on average basis. Nominal: Buy (debit cash from Account, credit Holding—same source/destination); Sell (reverse, calc gain/loss). Alternative: Multi-currency (e.g., USD ETF—apply FX rate).  
- **Transfer Between Accounts/Buckets**: Internal (e.g., Bank to Pension—no fee) or external (e.g., in-specie £60 fee). Nominal: Select source/destination; transfer Units/cash. Alternative: Bucket shift (e.g., 3 to 2—validate LDI); hybrid flows (e.g., UFPLS Withdrawal from Uncrystallised, then partial Crystallisation transfer to Drawdown sub-type—validate MPAA); phased pension liquidations (e.g., UFPLS partial to Crystallised Drawdown sub-type).  
- **Record Fees**: AUM/transaction/platform (e.g., 0.45% Pension). Nominal: Deduct from Destination Account cash (no source). Alternative: Recurring (link to Settings).  
- **Asset Adjustment (e.g., Property Valuation Update)**: Non-cash revision for personal Assets (e.g., reappraisal). Nominal: Select Asset (e.g., Bucket 4 Property); enter new value/date; system updates Holding (no double-entry). Alternative: Auto-suggest from growth rate; version for audit.  

**Alternatives (Generic):**  
API import: Stub validation (parse payload, match Account/category); apply if valid. Recategorize: Post-save edit with changelog (query updates tracker totals).  

**UML Activity (Generic Double-Entry):**  
[Start] → [Select Type] → [Decision Diamond: Type?] → [Credit: Destination only] / [Debit: Source only] / [Buy/Sell: Same Account] / [Transfer: Source/Destination] / [Fee: Destination only] → [Enter Details] → [Apply Debit/Credit + Cat/Tax] → [Decision: Valid/Balanced?] → [Yes: Version & Trigger Projections/Prompt Reassign] → [End] / [No: Alert & Edit]  

### UC-MGT-06: Assign Units to Buckets
**Description:** User assigns Units from Accounts to Buckets (1: Cash, 2: Pipeline, 3: Growth); Bucket 2 supports Release dates.  
**Preconditions:** Account with Units exists.  
**Postconditions:** Holding created in Bucket; validated for strategy.  
**Nominal Path:**  
1. User selects Units and target Bucket.  
2. System moves Units to Holding.  
3. If Bucket 2: User sets Release date.  
4. System validates (e.g., non-overlapping dates) and saves.  

**Initiation Pages:** Buckets, Accounts, Portfolio.  

**Alternatives:**  
Reassignment: Select existing Holding; update Bucket (validate no LDI conflicts).  
Prompted from Transaction: Post-liquidation (e.g., Bucket 2 sell), system alerts "Reassign proceeds to Bucket 1?"; user confirms manual shift.  
Phased for Pensions: Partial drawdown/UFPLS (e.g., 25% tax-free from uncrystallised); reassign only crystallised portion to Bucket 1 drawdown Account.  

### UC-MGT-07: Record Properties in Bucket 4
**Description:** User records personal Properties (e.g., rental home, not SIPP-held) in Bucket 4 with value, optional rental income, and disposal date; invoke adjustment for updates.  
**Preconditions:** Profile exists.  
**Postconditions:** Property added as Asset in Bucket 4; initial valuation set.  
**Nominal Path:**  
1. User enters Property details (e.g., address, initial value).  
2. Optionally: Set rental income schedule and disposal date.  
3. System invokes UC-MGT-05 for valuation confirmation and saves as Property Holding.  

**Initiation Pages:** Accounts, Buckets.  

**Alternatives:**  
Update: Edit existing (e.g., invoke UC-MGT-05 for reappraisal, adjust income for inflation).  

### UC-MGT-08: Create Budget Categories
**Description:** User creates Budget categories (defaults: "General", "Tax"); add custom (e.g., "Emergency").  
**Preconditions:** Profile exists.  
**Postconditions:** Budget category added to Profile.  
**Nominal Path:**  
1. User selects "Create Budget".  
2. User names category (or accepts default).  
3. System saves and links to Liabilities.  

**Initiation Pages:** Budgets, Dashboard.  

**Alternatives:**  
Custom: Specify period (e.g., annual) during step 2.  

### UC-MGT-09: Create Recurrent or Dated Items in Budgets
**Description:** User adds items to Budgets (e.g., monthly spending or one-off liability).  
**Preconditions:** Budget category exists.  
**Postconditions:** Liability item added, indexed for inflation if flagged.  
**Nominal Path:**  
1. User selects Budget and item type (recurrent/dated).  
2. User enters amount, date/period, and optional inflation index.  
3. System calculates projections and saves.  

**Initiation Pages:** Budgets, Income.  

**Alternatives:**  
Edit: Update existing item (e.g., adjust for lifestyle changes).  

### UC-MGT-10: Define External Sources of Income
**Description:** User defines Income sources (regular e.g., State Pension; ad-hoc e.g., inheritance); categories for credits ("Deposit" for initial cash, "Interest" for cash yields).  
**Preconditions:** Profile exists.  
**Postconditions:** Income source added, projected into forecasts.  
**Nominal Path:**  
1. User selects type (regular/ad-hoc) and category (e.g., "Deposit", "Interest", Pension, Rental).  
2. User enters details (e.g., amount, start date).  
3. System saves and adjusts for inflation if regular.  

**Initiation Pages:** Income, Budgets.  

**Alternatives:**  
Blend dated ad-hoc with regulars: Add one-off (e.g., £50k inheritance in 2027) to existing stream; system merges for net projections.  

### UC-MGT-11: Define Portfolio Settings
**Description:** User defines settings (e.g., RPI rate, pension drawdown strategy); includes Profile edits (Owners/tiers—user-selectable Basic/Premium/Advanced).  
**Preconditions:** Profile exists.  
**Postconditions:** Settings updated in Profile; logged with rollback option.  
**Nominal Path:**  
1. User navigates to Settings.  
2. User edits values (e.g., growth rate 5%, tier selection).  
3. System validates and saves (log change).  

**Initiation Pages:** Settings, Dashboard.  

**Alternatives:**  
Defaults: Load system presets (e.g., current RPI) on first access.  
Rollback: User selects "Revert" for logged change (e.g., tier to Basic).  

### UC-MGT-12: View Dashboards
**Description:** User views dashboards for Portfolio, Accounts, Assets, Buckets, and Budgets; includes Pension Access Summary (running MPAA/LSA).  
**Preconditions:** Profile with data exists.  
**Postconditions:** Data displayed; no changes.  
**Nominal Path:**  
1. User accesses landing dashboard.  
2. System aggregates and displays values (e.g., total Portfolio £X, Pension totals queried from history).  
3. User drills down if needed.  

**Initiation Pages:** Dashboard (landing).  

**Alternatives:**  
Simplified default view: High-level summaries (e.g., bucket balances only) for new users.  

## Forecasting Use Cases
[Placeholder - To be derived next; will include incremental refreshes using rules-engine for macro Bucket 3 growth scenarios (time/growth/budget-based), assuming rebalancing per rules; tax-inclusive (e.g., PAYE/CGT).]

### UC-FCT-01: View Portfolio Forecasts
**Description:** [TBD - Derived from item 1]

... [Similar placeholders for UC-FCT-02 to UC-FCT-08]

## Monitoring Use Cases
[Placeholder - To be derived; will tie to prompted reassignments from transactions and MPAA alerts.]

### UC-MON-01: Alert on Bucket 1 Cash Shortfall
**Description:** [TBD - Derived from item 1]

... [Similar placeholders for UC-MON-02 to UC-MON-10]

## Analysis Use Cases
[Placeholder - To be derived.]

### UC-ANL-01: View Portfolio Performance Analysis
**Description:** [TBD - Derived from item 1]

... [Similar placeholders for UC-ANL-02 to UC-ANL-05]

## Scenarios Use Cases
[Placeholder - To be derived; includes "What-If UFPLS" previews for MPAA/LSA simulations.]

### UC-SCN-01: Define and Analyse Scenarios
**Description:** [TBD - Derived from item 1; includes macro simulations with rules assumptions.]

## Operational Use Cases
[Placeholder - Non-functional; cross-cutting.]

## Interface Use Cases
[Placeholder - To be derived from Interface subsection; core pages: Dashboard, Settings (incl. Profile), Accounts (incl. Institutions/Properties/Catalogue access), Transactions, Buckets, Budgets, Income, Portfolio (sortable), Securities (Catalogue), Scenarios.]

*Traceability: UC-XXX-XX links to requirement items. Derived for class robustness. Pensions generalised; buckets prompted on transactions; MPAA/LSA queried from logs. Initiation Pages evolvable. Full expansion post-Management review.*
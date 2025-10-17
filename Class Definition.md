# Class-defs.md - High-Level UML Class Definitions for Retirement Portfolio Management System

## Introduction
This document specifies high-level UML class definitions derived from the Requirements Summary and Management use cases in HL Requirements.md and Use Cases.md. Classes are grouped by logical clusters (e.g., Core User, Account Management) for traceability. Each class includes a description and attributes table with visibility (+ public, - private), name, type, and constraints/notes mapped to use cases (e.g., UC-MGT-01 for Profile tier). Focus: Extensible framework for Management (e.g., polymorphic Account sub-types, Transaction source/destination) supporting full requirements (e.g., hooks for rules-engine in future Projection classes). Methods are high-level (e.g., addOwner() for alternatives). Relationships (e.g., associations) noted where relevant. Full Classes.mmd will compile in Mermaid notation post-validation. User and Profile merged into single Profile class with credentials and up to two Owners. Portfolio central for aggregation (Active/Scenario types) with 1:1 RulesSettings (replicable with deep copy for scenarios; definable independently, selectable via dropdown by name/description). Settings and rules in RulesSettings (composite: settings Map + rules List, with description/versionId).

## Core User Classes

### Profile
**Description**: Merged User/Profile entity representing the registered system account with credentials, financial data, and up to two Owners (independent of Owners for auth/settings). Supports authentication, joint ownership (UC-MGT-01 alternatives), and tier selection. Extensible for base defaults (e.g., inherited by Portfolios).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique identifier (auto-generated)            |
| +          | name                  | String            | Non-empty, full name (UC-MGT-01 step 1)       |
| -          | password              | String            | Hashed, minimum 8 chars (UC-MGT-01 step 1)    |
| +          | email                 | String            | Unique, validated format (e.g., user@example.com; UC-MGT-01 step 1) |
| +          | tier                  | Tier              | Enum: Basic, Premium, Advanced (UC-MGT-11 step 2, runtime checks e.g., canAccessAdvanced()) |
| +          | systemSettings        | Map<String, Object> | Account-level (e.g., "language": "en-GB"; UC-MGT-11 step 2) |
| +          | owners                | List<Owner>       | Up to 2, association (UC-MGT-01 alternatives; independent for auth) |
| +          | changeLog             | List<ChangeLogEntry> | Versioned edits for rollback (UC-MGT-11 alternative) |
| +          | portfolios            | List<Portfolio>   | 1:* association (Active/Scenarios; extensible) |
| +          | catalogue             | Catalogue         | 1:1 association (global per Profile; UC-MGT-03) |
| +          | authenticate(String password) : bool | -                 | Credential check (merged User auth)           |
| +          | addOwner(Owner) : void | -                 | For joint (UC-MGT-01 alternatives)            |
| +          | updateTier(Tier) : void | -                 | With logging (UC-MGT-11 step 3)               |
| +          | rollbackChange(String id) : void | -                 | Revert logged change (UC-MGT-11 alternative)  |
| +          | canAccessAdvanced() : bool | -                 | Tier-based check                              |

### Owner
**Description**: Represents an individual (primary or partner) owning Assets via Portfolio. Associated to Profile for joint access from UC-MGT-01 alternatives.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | name                  | String            | Non-empty (UC-MGT-01 step 1)                  |
| +          | dob                   | Date              | Valid date (UC-MGT-01 step 1)                 |
| +          | portfolio             | Portfolio         | Association (Terminology item 4, owns Assets) |

### Tier
**Description**: Enumeration for subscription levels, enabling runtime access checks (e.g., Premium for AI features in future Analysis).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| <<enumeration>> | Basic             | -                 | Default, core functionality only              |
| <<enumeration>> | Premium           | -                 | Additional features (e.g., advanced forecasts) |
| <<enumeration>> | Advanced          | -                 | Full AI, custom scenarios (future extensibility) |

### ChangeLogEntry
**Description**: Logs changes for rollback in Profile/Settings (UC-MGT-11 alternative). Extensible for audit in full system.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | timestamp             | Date              | Auto-set on change (UC-MGT-11 step 3)         |
| +          | changeType            | String            | e.g., "TierUpdate", "OwnerAdd"                |
| +          | oldValue              | Object            | Pre-change value (for rollback)               |
| +          | newValue              | Object            | Post-change value                             |

## Portfolio and RulesSettings Classes

### Portfolio
**Description**: Aggregates Units owned by Owners in Accounts and Buckets; Active for current, Scenario for what-ifs (cloned at baseDate). From Terminology item 7; associated with RulesSettings for per-portfolio variations. Extensible for replication (deep copy on scenario creation).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique identifier                             |
| +          | name                  | String            | Non-empty, e.g., "Family Portfolio", "Scenario 1" |
| +          | type                  | Enum              | Active, Scenario (UC-SCN-01 cloning)          |
| +          | accounts              | List<Account>     | 1:* association (UC-MGT-03)                   |
| +          | accountCategories     | Map<Owner, AccountType, Decimal> | Aggregated balances by owner and type for scenarios |
| +          | buckets               | List<Bucket>      | Cloned assignments, modifiable in Scenarios (UC-MGT-05) |
| +          | rulesSettings         | RulesSettings     | 1:1 association (selectable on creation/replication; deep copy for Scenarios) |
| +          | isVersioned           | Boolean           | True for Scenarios, enables save/compare      |
| +          | baseDate              | Date              | Snapshot date for Scenario portfolio balances |
| +          | snapshotValue         | Decimal           | Total portfolio value at baseDate (GBP)       |
| +          | clone() : Portfolio   | -                 | For Scenario creation (extensible)            |

### RulesSettings
**Description**: Composite for portfolio-specific settings and rules; definable independently, selectable for Portfolios (dropdown by name/description). From item 9 (Settings) and item 21 (Rules Engine); extensible for replication (deep copy on scenario creation).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique identifier                             |
| +          | name                  | String            | Non-empty, e.g., "Conservative Rules" (user-selectable dropdown) |
| +          | description           | String            | Optional, e.g., "Low risk thresholds" (for replication previews) |
| +          | versionId             | String            | Auto-increment on changes (for tracking)      |
| +          | settings              | Map<String, Decimal> | Key-value params (e.g., "RPI": 0.02; inherits from Profile base) |
| +          | rules                 | List<Rule>        | Guardrails/conditions (e.g., threshold expressions; item 21) |
| +          | portfolios            | List<Portfolio>   | 1:* association (applied to multiple, selectable on creation) |
| +          | cloneFrom(RulesSettings source) : RulesSettings | -                 | Deep copy for scenarios (UC-SCN-01 replication) |

### Rule
**Description**: Parameterised condition/guardrail in RulesSettings (e.g., pipeline length threshold). Extensible for Monitoring alerts.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique                                        |
| +          | type                  | String            | e.g., "Threshold" (item 22 Conditions)        |
| +          | expression            | String            | Parameterised (e.g., "pipelineLength > 2 years") |
| +          | parameters            | Map<String, Object> | e.g., "minLength": 2 (item 22)                |

## Catalogue and Asset Classes

### Catalogue
**Description**: Global user-selected group of Assets for brokerage transactions (one per Profile). From Terminology item 26; association to Assets (loose, Assets reusable across contexts).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | profile               | Profile           | 1:1 association (global per Profile; UC-MGT-03) |
| +          | assets                | List<Asset>       | Association (loose reference, UC-MGT-03 step 3) |
| +          | addAsset(Asset) : void | -                 | Validate/enrich (stubbed API, UC-MGT-03 step 2) |
| +          | editAsset(String id, Asset updates) : void | -                 | If unused in transactions (UC-MGT-03 alternative) |
| +          | isDeletable(Asset) : bool | -                 | False post-transaction (traceability, UC-MGT-03 alternative) |

### Asset
**Description**: Transactable instrument (cash, securities, property). General base for Catalogue (UC-MGT-03) and Holdings (UC-MGT-05).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | name                  | String            | e.g., "Vanguard ETF" (freeform or enriched, UC-MGT-03 step 2) |
| +          | type                  | AssetType         | Enum: Cash, Security, Property (Terminology item 5) |
| +          | ticker                | String            | Optional for securities (UC-MGT-03 step 1)    |
| +          | currentPrice          | Decimal           | Per unit, from market stub (future)           |

### Holding
**Description**: Value of Asset Units in Account/Bucket. From UC-MGT-05 nominal (assignment).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique                                        |
| +          | asset                 | Asset             | Association (UC-MGT-05 step 2)                |
| +          | account               | Account           | Association (UC-MGT-05 step 1)                |
| +          | bucket                | Bucket            | Optional association (UC-MGT-05 step 1)       |
| +          | quantity              | Decimal           | Units held (UC-MGT-05 step 1)                 |
| +          | value                 | Decimal           | Quantity * price (updated on transactions)    |
| +          | updateValue() : void  | -                 | Recalc on adjustment (UC-MGT-05f)             |

### Property
**Description**: Sub-type of Asset for Bucket 4 (personal residence/rental). From UC-MGT-06 nominal.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | address               | String            | Non-empty (UC-MGT-06 step 1)                  |
| +          | rentalIncomeSchedule  | List<Date, Decimal> | Optional recurrent (UC-MGT-06 step 2)         |
| +          | disposalDate          | Date              | Optional (UC-MGT-06 step 2)                   |

## Income and Budget Classes

### IncomeSource
**Description**: External income (regular/ad-hoc) with categories ("Deposit", "Interest", Pension, Rental). From UC-MGT-10 nominal.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | type                  | String            | Regular/Ad-hoc (UC-MGT-10 step 1)             |
| +          | category              | Category          | e.g., "Deposit" initial cash, "Interest" cash yields (UC-MGT-10 step 1) |
| +          | amount                | Decimal           | Initial value (UC-MGT-10 step 2)              |
| +          | startDate             | Date              | (UC-MGT-10 step 2)                            |
| +          | isInflationAdjusted   | Boolean           | True for regulars (UC-MGT-10 step 3)          |
| +          | profile               | Profile           | Association (UC-MGT-10 step 3)                |

### Budget
**Description**: Grouping of Liabilities for spending categories. From UC-MGT-07 nominal.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | name                  | String            | e.g., "General", "Tax" (UC-MGT-07 step 2)     |
| +          | period                | Enum              | Monthly, Annual (UC-MGT-07 alternative)       |
| +          | liabilities           | List<Liability>   | Association (UC-MGT-08 step 3)                |
| +          | isInflationAdjusted   | Boolean           | Default false (UC-MGT-08 step 2)              |

### Liability
**Description**: Spending obligation in Budget. From UC-MGT-08 nominal.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | amount                | Decimal           | Target spend (UC-MGT-08 step 2)               |
| +          | date                  | Date              | For dated (UC-MGT-08 step 2)                  |
| +          | isRecurrent           | Boolean           | True for monthly (UC-MGT-08 step 1)           |
| +          | budget                | Budget            | Association (UC-MGT-08 step 1)                |

## Transaction and Category Classes

### Transaction
**Description**: Records events affecting Accounts/Buckets with double-entry; polymorphic for types (credits/debits, buys/sells, transfers, fees, adjustments). Source/destination optional per type; flags for pension (e.g., UFPLS). From UC-MGT-04 nominal, with validation diamond.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | type                  | TransactionType   | Enum: Credit, Debit, Buy, Sell, Transfer, Fee, Adjustment (UC-MGT-04 step 1) |
| +          | amount                | Decimal           | Positive value in GBP (UC-MGT-04 step 2)      |
| +          | currency              | String            | Default GBP; FX rate if multi (UC-MGT-04 step 2) |
| +          | sourceAccount         | Account           | Optional for credits (external), required for debits/transfers (UC-MGT-04 decision diamond) |
| +          | destinationAccount    | Account           | Optional for debits (external), required for credits/transfers (UC-MGT-04 decision diamond) |
| +          | category              | Category          | Income/Debit link (e.g., "Deposit", "Tax"; UC-MGT-04 step 2) |
| +          | fees                  | Decimal           | Fixed/% (UC-MGT-04 step 2)                    |
| +          | tax                   | Decimal           | Manual entry for PAYE/CGT (UC-MGT-04 step 2)  |
| +          | timestamp             | Date              | Auto-set (UC-MGT-04 step 4)                   |
| +          | flag                  | String            | e.g., "UFPLS Withdrawal" for pension (UC-MGT-04 alternative) |
| +          | versionId             | String            | For changelog (UC-MGT-04 postconditions)      |
| +          | applyDoubleEntry() : bool | -                 | Validate balances, update Holdings (UC-MGT-04 step 3) |
| +          | queryTrackerTotal(String flag) : Decimal | -                 | Aggregate MPAA/LSA from history (UC-MGT-04 postconditions) |

### Category
**Description**: Defines types for credits/debits (e.g., "Deposit", "Interest" for Income; "Tax" for debits with deduction). From UC-MGT-10 and UC-MGT-04 step 2.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | name                  | String            | e.g., "Deposit", "Interest", "Tax" (UC-MGT-10 step 1) |
| +          | type                  | CategoryType      | Enum: Income, Debit (UC-MGT-04 step 2)        |
| +          | isInflationAdjusted   | Boolean           | True for regulars like Interest (UC-MGT-10 step 3) |
| +          | deductFromCash        | Boolean           | True for Tax (manual deduction, UC-MGT-04 step 3) |

### TransactionType
**Description**: Enumeration for transaction variants, enabling polymorphism (e.g., source/destination rules).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| <<enumeration>> | Credit             | -                 | Destination only (external inflow)            |
| <<enumeration>> | Debit              | -                 | Source only (external outflow)                |
| <<enumeration>> | Buy                | -                 | Same account (cash to Holding)                |
| <<enumeration>> | Sell               | -                 | Same account (Holding to cash)                |
| <<enumeration>> | Transfer           | -                 | Source/destination separate (e.g., pension phases) |
| <<enumeration>> | Fee                | -                 | Destination only (deduct cash)                |
| <<enumeration>> | Adjustment         | -                 | Non-cash (e.g., property value update)        |

## Asset and Catalogue Classes

### Catalogue
**Description**: Global user-selected group of Assets for brokerage transactions (one per Profile). From Terminology item 26; association to Assets (loose, Assets reusable across contexts).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | profile               | Profile           | 1:1 association (global per Profile; UC-MGT-03) |
| +          | assets                | List<Asset>       | Association (loose reference, UC-MGT-03 step 3) |
| +          | addAsset(Asset) : void | -                 | Validate/enrich (stubbed API, UC-MGT-03 step 2) |
| +          | editAsset(String id, Asset updates) : void | -                 | If unused in transactions (UC-MGT-03 alternative) |
| +          | isDeletable(Asset) : bool | -                 | False post-transaction (traceability, UC-MGT-03 alternative) |

### Asset
**Description**: Transactable instrument (cash, securities, property). General base for Catalogue (UC-MGT-03) and Holdings (UC-MGT-05).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | name                  | String            | e.g., "Vanguard ETF" (freeform or enriched, UC-MGT-03 step 2) |
| +          | type                  | AssetType         | Enum: Cash, Security, Property (Terminology item 5) |
| +          | ticker                | String            | Optional for securities (UC-MGT-03 step 1)    |
| +          | currentPrice          | Decimal           | Per unit, from market stub (future)           |

### Holding
**Description**: Value of Asset Units in Account/Bucket. From UC-MGT-05 nominal (assignment).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique                                        |
| +          | asset                 | Asset             | Association (UC-MGT-05 step 2)                |
| +          | account               | Account           | Association (UC-MGT-05 step 1)                |
| +          | bucket                | Bucket            | Optional association (UC-MGT-05 step 1)       |
| +          | quantity              | Decimal           | Units held (UC-MGT-05 step 1)                 |
| +          | value                 | Decimal           | Quantity * price (updated on transactions)    |
| +          | updateValue() : void  | -                 | Recalc on adjustment (UC-MGT-05f)             |

### Property
**Description**: Sub-type of Asset for Bucket 4 (personal residence/rental). From UC-MGT-06 nominal.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | address               | String            | Non-empty (UC-MGT-06 step 1)                  |
| +          | rentalIncomeSchedule  | List<Date, Decimal> | Optional recurrent (UC-MGT-06 step 2)         |
| +          | disposalDate          | Date              | Optional (UC-MGT-06 step 2)                   |

## Income and Budget Classes

### IncomeSource
**Description**: External income (regular/ad-hoc) with categories ("Deposit", "Interest", Pension, Rental). From UC-MGT-10 nominal.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | type                  | String            | Regular/Ad-hoc (UC-MGT-10 step 1)             |
| +          | category              | Category          | e.g., "Deposit" initial cash, "Interest" cash yields (UC-MGT-10 step 1) |
| +          | amount                | Decimal           | Initial value (UC-MGT-10 step 2)              |
| +          | startDate             | Date              | (UC-MGT-10 step 2)                            |
| +          | isInflationAdjusted   | Boolean           | True for regulars (UC-MGT-10 step 3)          |
| +          | profile               | Profile           | Association (UC-MGT-10 step 3)                |

### Budget
**Description**: Grouping of Liabilities for spending categories. From UC-MGT-07 nominal.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | name                  | String            | e.g., "General", "Tax" (UC-MGT-07 step 2)     |
| +          | period                | Enum              | Monthly, Annual (UC-MGT-07 alternative)       |
| +          | liabilities           | List<Liability>   | Association (UC-MGT-08 step 3)                |
| +          | isInflationAdjusted   | Boolean           | Default false (UC-MGT-08 step 2)              |

### Liability
**Description**: Spending obligation in Budget. From UC-MGT-08 nominal.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | amount                | Decimal           | Target spend (UC-MGT-08 step 2)               |
| +          | date                  | Date              | For dated (UC-MGT-08 step 2)                  |
| +          | isRecurrent           | Boolean           | True for monthly (UC-MGT-08 step 1)           |
| +          | budget                | Budget            | Association (UC-MGT-08 step 1)                |

## Transaction and Category Classes

### Transaction
**Description**: Records events affecting Accounts/Buckets with double-entry; polymorphic for types (credits/debits, buys/sells, transfers, fees, adjustments). Source/destination optional per type; flags for pension (e.g., UFPLS). From UC-MGT-04 nominal, with validation diamond.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | type                  | TransactionType   | Enum: Credit, Debit, Buy, Sell, Transfer, Fee, Adjustment (UC-MGT-04 step 1) |
| +          | amount                | Decimal           | Positive value in GBP (UC-MGT-04 step 2)      |
| +          | currency              | String            | Default GBP; FX rate if multi (UC-MGT-04 step 2) |
| +          | sourceAccount         | Account           | Optional for credits (external), required for debits/transfers (UC-MGT-04 decision diamond) |
| +          | destinationAccount    | Account           | Optional for debits (external), required for credits/transfers (UC-MGT-04 decision diamond) |
| +          | category              | Category          | Income/Debit link (e.g., "Deposit", "Tax"; UC-MGT-04 step 2) |
| +          | fees                  | Decimal           | Fixed/% (UC-MGT-04 step 2)                    |
| +          | tax                   | Decimal           | Manual entry for PAYE/CGT (UC-MGT-04 step 2)  |
| +          | timestamp             | Date              | Auto-set (UC-MGT-04 step 4)                   |
| +          | flag                  | String            | e.g., "UFPLS Withdrawal" for pension (UC-MGT-04 alternative) |
| +          | versionId             | String            | For changelog (UC-MGT-04 postconditions)      |
| +          | applyDoubleEntry() : bool | -                 | Validate balances, update Holdings (UC-MGT-04 step 3) |
| +          | queryTrackerTotal(String flag) : Decimal | -                 | Aggregate MPAA/LSA from history (UC-MGT-04 postconditions) |

### Category
**Description**: Defines types for credits/debits (e.g., "Deposit", "Interest" for Income; "Tax" for debits with deduction). From UC-MGT-10 and UC-MGT-04 step 2.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | name                  | String            | e.g., "Deposit", "Interest", "Tax" (UC-MGT-10 step 1) |
| +          | type                  | CategoryType      | Enum: Income, Debit (UC-MGT-04 step 2)        |
| +          | isInflationAdjusted   | Boolean           | True for regulars like Interest (UC-MGT-10 step 3) |
| +          | deductFromCash        | Boolean           | True for Tax (manual deduction, UC-MGT-04 step 3) |

### TransactionType
**Description**: Enumeration for transaction variants, enabling polymorphism (e.g., source/destination rules).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| <<enumeration>> | Credit             | -                 | Destination only (external inflow)            |
| <<enumeration>> | Debit              | -                 | Source only (external outflow)                |
| <<enumeration>> | Buy                | -                 | Same account (cash to Holding)                |
| <<enumeration>> | Sell               | -                 | Same account (Holding to cash)                |
| <<enumeration>> | Transfer           | -                 | Source/destination separate (e.g., pension phases) |
| <<enumeration>> | Fee                | -                 | Destination only (deduct cash)                |
| <<enumeration>> | Adjustment         | -                 | Non-cash (e.g., property value update)        |

## Portfolio and RulesSettings Classes

### Portfolio
**Description**: Aggregates Units owned by Owners in Accounts and Buckets; Active for current, Scenario for what-ifs (cloned at baseDate). From Terminology item 7; associated with RulesSettings for per-portfolio variations. Extensible for replication (deep copy on scenario creation).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique identifier                             |
| +          | name                  | String            | Non-empty, e.g., "Family Portfolio", "Scenario 1" |
| +          | type                  | Enum              | Active, Scenario (UC-SCN-01 cloning)          |
| +          | accounts              | List<Account>     | 1:* association (UC-MGT-03)                   |
| +          | accountCategories     | Map<Owner, AccountType, Decimal> | Aggregated balances by owner and type for scenarios |
| +          | buckets               | List<Bucket>      | Cloned assignments, modifiable in Scenarios (UC-MGT-05) |
| +          | rulesSettings         | RulesSettings     | 1:1 association (selectable on creation/replication; deep copy for Scenarios) |
| +          | isVersioned           | Boolean           | True for Scenarios, enables save/compare      |
| +          | baseDate              | Date              | Snapshot date for Scenario portfolio balances |
| +          | snapshotValue         | Decimal           | Total portfolio value at baseDate (GBP)       |
| +          | clone() : Portfolio   | -                 | For Scenario creation (extensible)            |

### RulesSettings
**Description**: Composite for portfolio-specific settings and rules; definable independently, selectable for Portfolios (dropdown by name/description). From item 9 (Settings) and item 21 (Rules Engine); extensible for replication (deep copy on scenario creation).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique identifier                             |
| +          | name                  | String            | Non-empty, e.g., "Conservative Rules" (user-selectable dropdown) |
| +          | description           | String            | Optional, e.g., "Low risk thresholds" (for replication previews) |
| +          | versionId             | String            | Auto-increment on changes (for tracking)      |
| +          | settings              | Map<String, Decimal> | Key-value params (e.g., "RPI": 0.02; inherits from Profile base) |
| +          | rules                 | List<Rule>        | Guardrails/conditions (e.g., threshold expressions; item 21) |
| +          | portfolios            | List<Portfolio>   | 1:* association (applied to multiple, selectable on creation) |
| +          | cloneFrom(RulesSettings source) : RulesSettings | -                 | Deep copy for scenarios (UC-SCN-01 replication) |

### Rule
**Description**: Parameterised condition/guardrail in RulesSettings (e.g., pipeline length threshold). Extensible for Monitoring alerts.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique                                        |
| +          | type                  | String            | e.g., "Threshold" (item 22 Conditions)        |
| +          | expression            | String            | Parameterised (e.g., "pipelineLength > 2 years") |
| +          | parameters            | Map<String, Object> | e.g., "minLength": 2 (item 22)                |

## Account Management Classes

### Institution
**Description**: Provider of Accounts (e.g., bank/brokerage) from UC-MGT-02. Association to multiple Accounts for overlaps.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | name                  | String            | Non-empty, e.g., "Hargreaves Lansdown" (UC-MGT-02 step 1) |
| +          | type                  | Enum              | Bank, Brokerage (UC-MGT-02 step 1)            |
| +          | accounts              | List<Account>     | Association (1:* , UC-MGT-03 step 1)          |

### Account
**Description**: Holds Units, owned by Owners; polymorphic base for sub-types (Bank: Current/Deposit; Brokerage: Trading/ISA/Pension). From UC-MGT-03 nominal, with sub-type prompts.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | type                  | AccountType       | Enum: Bank, Brokerage (UC-MGT-03 step 2)      |
| +          | subType               | String            | e.g., "Current", "Trading", "Uncrystallised" (guided prompt, UC-MGT-03 step 2) |
| +          | institution           | Institution       | Association (UC-MGT-03 step 5)                |
| +          | owners                | List<Owner>       | Joint flag (UC-MGT-03 alternative)            |
| +          | balance               | Decimal           | Current value in GBP (updated on transactions) |
| +          | getSubTypeValidation() : bool | -                 | Runtime check for sub-type rules (extensible) |

### BankAccount
**Description**: Sub-type of Account for cash holdings (Current for spending, Deposit for low-volatility). Extends Account for UC-MGT-03 sub-types.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | interestRate          | Decimal           | Annual % (for "Interest" category projections) |
| +          | isCurrent             | Boolean           | True for Current sub-type (UC-MGT-03 step 2)  |

### BrokerageAccount
**Description**: Sub-type of Account for securities/cash; further sub-types Trading/ISA/Pension. Supports Catalogue additions from UC-MGT-04c.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | holdings              | List<Holding>     | Association to Assets in buckets (UC-MGT-05)  |
| +          | getTaxWrapper() : String | -                 | e.g., "ISA" for tax-free (UC-MGT-03 validation) |

### PensionAccount
**Description**: Sub-type of BrokerageAccount for pension pots; sub-types Uncrystallised/Crystallised Drawdown. Flags for phases from UC-MGT-04d transfers.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | phase                 | String            | "Uncrystallised", "Crystallised Drawdown" (UC-MGT-03 step 2) |
| +          | mpaaUsed              | Decimal           | Running total queried from transactions (UC-MGT-04 postconditions) |
| +          | lsaUsed               | Decimal           | Lifetime allowance deduction (UC-MGT-04 postconditions) |
| +          | validateWithdrawal(Decimal amount) : bool | -                 | Check MPAA/LSA on flags (UC-MGT-04 step 4)    |

## Transaction and Category Classes

### Transaction
**Description**: Records events affecting Accounts/Buckets with double-entry; polymorphic for types (credits/debits, buys/sells, transfers, fees, adjustments). Source/destination optional per type; flags for pension (e.g., UFPLS). From UC-MGT-04 nominal, with validation diamond.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | type                  | TransactionType   | Enum: Credit, Debit, Buy, Sell, Transfer, Fee, Adjustment (UC-MGT-04 step 1) |
| +          | amount                | Decimal           | Positive value in GBP (UC-MGT-04 step 2)      |
| +          | currency              | String            | Default GBP; FX rate if multi (UC-MGT-04 step 2) |
| +          | sourceAccount         | Account           | Optional for credits (external), required for debits/transfers (UC-MGT-04 decision diamond) |
| +          | destinationAccount    | Account           | Optional for debits (external), required for credits/transfers (UC-MGT-04 decision diamond) |
| +          | category              | Category          | Income/Debit link (e.g., "Deposit", "Tax"; UC-MGT-04 step 2) |
| +          | fees                  | Decimal           | Fixed/% (UC-MGT-04 step 2)                    |
| +          | tax                   | Decimal           | Manual entry for PAYE/CGT (UC-MGT-04 step 2)  |
| +          | timestamp             | Date              | Auto-set (UC-MGT-04 step 4)                   |
| +          | flag                  | String            | e.g., "UFPLS Withdrawal" for pension (UC-MGT-04 alternative) |
| +          | versionId             | String            | For changelog (UC-MGT-04 postconditions)      |
| +          | applyDoubleEntry() : bool | -                 | Validate balances, update Holdings (UC-MGT-04 step 3) |
| +          | queryTrackerTotal(String flag) : Decimal | -                 | Aggregate MPAA/LSA from history (UC-MGT-04 postconditions) |

### Category
**Description**: Defines types for credits/debits (e.g., "Deposit", "Interest" for Income; "Tax" for debits with deduction). From UC-MGT-10 and UC-MGT-04 step 2.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | name                  | String            | e.g., "Deposit", "Interest", "Tax" (UC-MGT-10 step 1) |
| +          | type                  | CategoryType      | Enum: Income, Debit (UC-MGT-04 step 2)        |
| +          | isInflationAdjusted   | Boolean           | True for regulars like Interest (UC-MGT-10 step 3) |
| +          | deductFromCash        | Boolean           | True for Tax (manual deduction, UC-MGT-04 step 3) |

### TransactionType
**Description**: Enumeration for transaction variants, enabling polymorphism (e.g., source/destination rules).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| <<enumeration>> | Credit             | -                 | Destination only (external inflow)            |
| <<enumeration>> | Debit              | -                 | Source only (external outflow)                |
| <<enumeration>> | Buy                | -                 | Same account (cash to Holding)                |
| <<enumeration>> | Sell               | -                 | Same account (Holding to cash)                |
| <<enumeration>> | Transfer           | -                 | Source/destination separate (e.g., pension phases) |
| <<enumeration>> | Fee                | -                 | Destination only (deduct cash)                |
| <<enumeration>> | Adjustment         | -                 | Non-cash (e.g., property value update)        |

## Asset and Catalogue Classes

### Catalogue
**Description**: Global user-selected group of Assets for brokerage transactions (one per Profile). From Terminology item 26; association to Assets (loose, Assets reusable across contexts).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | profile               | Profile           | 1:1 association (global per Profile; UC-MGT-03) |
| +          | assets                | List<Asset>       | Association (loose reference, UC-MGT-03 step 3) |
| +          | addAsset(Asset) : void | -                 | Validate/enrich (stubbed API, UC-MGT-03 step 2) |
| +          | editAsset(String id, Asset updates) : void | -                 | If unused in transactions (UC-MGT-03 alternative) |
| +          | isDeletable(Asset) : bool | -                 | False post-transaction (traceability, UC-MGT-03 alternative) |

### Asset
**Description**: Transactable instrument (cash, securities, property). General base for Catalogue (UC-MGT-03) and Holdings (UC-MGT-05).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | name                  | String            | e.g., "Vanguard ETF" (freeform or enriched, UC-MGT-03 step 2) |
| +          | type                  | AssetType         | Enum: Cash, Security, Property (Terminology item 5) |
| +          | ticker                | String            | Optional for securities (UC-MGT-03 step 1)    |
| +          | currentPrice          | Decimal           | Per unit, from market stub (future)           |

### Holding
**Description**: Value of Asset Units in Account/Bucket. From UC-MGT-05 nominal (assignment).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique                                        |
| +          | asset                 | Asset             | Association (UC-MGT-05 step 2)                |
| +          | account               | Account           | Association (UC-MGT-05 step 1)                |
| +          | bucket                | Bucket            | Optional association (UC-MGT-05 step 1)       |
| +          | quantity              | Decimal           | Units held (UC-MGT-05 step 1)                 |
| +          | value                 | Decimal           | Quantity * price (updated on transactions)    |
| +          | updateValue() : void  | -                 | Recalc on adjustment (UC-MGT-05f)             |

### Property
**Description**: Sub-type of Asset for Bucket 4 (personal residence/rental). From UC-MGT-06 nominal.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | address               | String            | Non-empty (UC-MGT-06 step 1)                  |
| +          | rentalIncomeSchedule  | List<Date, Decimal> | Optional recurrent (UC-MGT-06 step 2)         |
| +          | disposalDate          | Date              | Optional (UC-MGT-06 step 2)                   |

## Income and Budget Classes

### IncomeSource
**Description**: External income (regular/ad-hoc) with categories ("Deposit", "Interest", Pension, Rental). From UC-MGT-10 nominal.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | type                  | String            | Regular/Ad-hoc (UC-MGT-10 step 1)             |
| +          | category              | Category          | e.g., "Deposit" initial cash, "Interest" cash yields (UC-MGT-10 step 1) |
| +          | amount                | Decimal           | Initial value (UC-MGT-10 step 2)              |
| +          | startDate             | Date              | (UC-MGT-10 step 2)                            |
| +          | isInflationAdjusted   | Boolean           | True for regulars (UC-MGT-10 step 3)          |
| +          | profile               | Profile           | Association (UC-MGT-10 step 3)                |

### Budget
**Description**: Grouping of Liabilities for spending categories. From UC-MGT-07 nominal.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | name                  | String            | e.g., "General", "Tax" (UC-MGT-07 step 2)     |
| +          | period                | Enum              | Monthly, Annual (UC-MGT-07 alternative)       |
| +          | liabilities           | List<Liability>   | Association (UC-MGT-08 step 3)                |
| +          | isInflationAdjusted   | Boolean           | Default false (UC-MGT-08 step 2)              |

### Liability
**Description**: Spending obligation in Budget. From UC-MGT-08 nominal.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | id                    | String            | Unique (auto-generated)                       |
| +          | amount                | Decimal           | Target spend (UC-MGT-08 step 2)               |
| +          | date                  | Date              | For dated (UC-MGT-08 step 2)                  |
| +          | isRecurrent           | Boolean           | True for monthly (UC-MGT-08 step 1)           |
| +          | budget                | Budget            | Association (UC-MGT-08 step 1)                |

*Traceability: Classes map to Management use cases (e.g., Transaction to UC-MGT-04). Extensible for Forecasting (e.g., Projection queries Transaction logs). Full Classes.mmd in Mermaid post-validation.*
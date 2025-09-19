**Retirement Portfolio Management System - Requirements Specification**
=====================================================================
# Introduction
This document specifies the high-level requirements for a retirement portfolio management system. 

It assumes familiarity with terminology and concepts associated with portfolio management, finance, UML methodologies, UX design, SQL and software coding.

It is intended as a reference for AI chatbots so they may assist in the analysis, design and implementation of the system. 

## Background

Retirees face the challenge of generating a reliable and sufficient income stream from their portfolio while preserving capital for inheritance. This process is complex, stressful, time-consuming, and prone to errors that can lead to cash shortfalls. Although a variety of software tools tackle discrete tasks such as rebalancing, forecasting and budgeting, none provide individual investors with full support for the implementation of a practical portfolio management strategy.

This system fills that gap by providing users with means of implementing a comprehensive, flexible, rules-based strategy that is based on best practice. It provides unified, AI enabled features for portfolio management, monitoring, and analysis that help users achieve their financial goals.

## Overview

The System implements a hybrid Bucket and Liability-Driven Investment (LDI) strategy that incorporates guardrails. This is intended to mitigate market volatility and aid financial planning by structuring a portfolio into time-segmented groupings ("Buckets") whose size and composition are determined by budgeted cash-flow requirements. The system continuously analyses and monitors the portfolio to provide information that helps users make timely decisions. It does not provide investments recommendations or advice and it does not execute trades or move money.

The system is based around four buckets
- Bucket 1 holds cash for spending 
- Bucket 2 holds medium term, low-volatility assets to provide a pipeline of income
- Bucket 3 holds long term assets to generate growth
- Bucket 4 (optional) holds rental property that provides rental income and generates growth

The growth generated from assets in Bucket 3 is used to purchase assets for Bucket 2 which provide a scheduled stream of income for Bucket 1. Bucket 4 may be used to provide additional income for Bucket 1.

Cash in Bucket 1 is monitored against rolling budgets and guardrails to create alerts that notify users if a shortfall is forecast thereby enabling them to take actions such as reducing spending or topping-up.

Assets in Bucket 2 can be structured into an income pipeline that consists of a sequence of scheduled release events (“Releases”), each with a defined value and date. Each release is intended to transfer sufficient cash into Bucket 1 to support the forecast budget demands until the next scheduled release. The number of releases determines how many years of spending are supported by the  pipeline. Assets that are not in the pipeline (ie have no release date associated with them) can also be liquidated and transferred to Bucket 1 on an ad-hoc basis (eg if there is am interim shortfall in Bucket 1). Each release in the pipeline may contain a combination of 
- Bonds, whose maturity date determines the release date
- Other securities with a user-defined release date
- Scheduled dividend or interest payments 
  
Asset in Bucket 3 are periodically liquidated to replenish Bucket 2. The size, timing, and selection of liquidations are determined by asset performance, future liabilities, shortfall risk, pipeline length, guardrails, tax efficiency and market conditions. The system issues four levels of alert to indicate when liquidation actions should be taken: green (no action), amber (optional replenishment), red (required replenishment), and purple (optional profit-taking). Each alert is accompanied by specific recommendations that ensure decisions are disciplined yet adaptable.

The system allows users create to budgets for different spending categories, each representing a future liability. Budgets are tracked against actual spending.A default “Everyday” budget, calculated from historic spending, covers ongoing living expenses. Users can add budgets for defined categories such as “Major Purchases” or “Emergency Fund.” Items within budgets can be indexed for inflation and lifestyle changes so that new release values can be calculated and long-term liabilities can be projected for analysis and forecasting.

The system allows users to create projections for income from external sources. This includes regular income such as state pension, rental income and annuity payments (which can be adjusted for inflation) and one-off events such as a house sale or inheritance.

A rolling cashflow forecast is calculated from the cash in Bucket 1, the scheduled liabilities from Budgets and taxation and the projected income from Bucket 2 and external sources. The forecast shows whether Bucket 1 holds sufficient cash and, if not, calculates the required top-up. The cashflow forecast is also used to calculate the required size and release-dates for new releases in the income pipeline.

The system maintains up-to-date balances for all user accounts, buckets and budgets by recording transactions and accessing online financial data. Transactions capture every event that affects the portfolio: spending, external income, the purchase and sale of assets, dividend & interest payments, transfers, fees and adjustments etc.  Each transaction stores full details required for precise accounting and analysis: trade and settlement dates, source and destination accounts, currency and applicable exchange rate, linked security or budget category, amount, quantity and unit price, plus any fees, taxes, or accrued income. Every entry carries a unique identifier and is recorded using a double-entry structure to ensure balances remain consistent. Users enter transactions manually, but future enhancements may allow automation through direct bank or broker feeds, CSV uploads, or API integrations. All edits are versioned with a full change log to provide an auditable history.

The system provides a suite of tools (some enhanced by AI) that let users monitor, analyse, and forecast portfolio performance and assist in tax planning. These tools draw on the full transaction history and current market data to calculate returns, risk metrics, cash-flow projections, and asset allocations. Users can run scenario tests, perform risk and stress testing and model alternative investment or withdrawal strategies, enabling evidence-based decisions and forward-looking planning.

# Competition

The following tools provide comparable features and functionality that should be examined to inform decisions about the requirements, design and implementation of the system.

| Name                                | Primary Market                          | Strengths                                                                 | Weaknesses                                                              |
|-------------------------------------|-----------------------------------------|---------------------------------------------------------------------------|-------------------------------------------------------------------------|
| ProjectionLab                      | Individual Investor                     | Advanced Monte Carlo simulations, customizable scenarios, Social Security optimization, strong for what-if analysis and tax modeling, International support | Steeper learning curve; less focus on pure bucket strategy visualizations. |
| Boldin    | Individual Investor                     | Holistic planning including Medicare, relocation; Roth conversions, consumption smoothing; user-friendly with community support. | Less specialized in bucket strategy visualizations; some advanced features require deeper setup. USA focussed|
| Maxifi Planner                     | Individual Investor                     | Economic modeling for steady spending, strong tax strategies, ACA/Roth integration. | Complex interface; focused on consumption smoothing rather than visual buckets. USA focussed|
| Pralana Retirement Planner         | Individual Investor                     | Excel-based with detailed simulations, historical/Monte Carlo analysis, strong for advanced users. | Complex setup; spreadsheet-heavy. |
| Empower  | Individual Investor                     | Free portfolio tracking, retirement forecasting, tax harvesting; integrated with banking/investing. | Less emphasis on bucket-specific strategies; tied to advisory ecosystem. |
| The Complete Retirement Planner (TCRP) | Individual Investor                     | Comprehensive pre- and post-retirement planning, customizable spreadsheets. | Spreadsheet-based; requires manual updates; less automated than web-based tools. |
| Big ERN’s SWR Toolbox              | Individual Investor                     | Safe withdrawal rate simulations, bucket strategy adaptations via spreadsheets; free to use. | Spreadsheet-only; requires manual input; limited to SWR focus without full portfolio tracking. |
| Charles Schwab’s Retirement Calculator | Individual Investor                     | Savings projections, expense coverage, bucket-like forecasting; integrated with Schwab accounts. | Basic calculator; lacks advanced customization; tied to Schwab ecosystem. |
| Fidelity’s Planning & Guidance Center | Individual Investor                     | Retirement income planning, portfolio analysis, tax-efficient strategies; free tools with advisory add-ons. | Ecosystem-locked to Fidelity accounts; not pure bucket-focused. |
| BucketsFP                          | Professional (Advisors)                 | Intuitive bucket strategy visualization, tax-aware withdrawal planning, advisor-client reporting. | Limited advanced simulations (e.g., no Monte Carlo) |
| Bucket Bliss Advisor               | Professional (Advisors)                 | Direct bucket strategy implementation, simple visualizations, marketing tools for advisors. | Limited user reviews; more advisor-focused, less intuitive for individuals; basic compared to comprehensive tools. |
| RightCapital                       | Professional (Advisors)                 | Bucket-style scenarios, stress testing, client engagement tools, Roth modeling. | Expensive for individuals; requires professional setup. |
| BlackRock’s iReTIRE                | Professional (Advisors/Institutions)    | Risk analysis, scenario modeling, tax optimization using Aladdin platform; strong for bucket-style withdrawals. | Limited to institutional users; high AUM requirements; less DIY-friendly. |
| Blackbaud Financial Edge NXT      | Endowment Fund/Nonprofit                | Bucket-like asset allocation across liquidity pools, donor intent compliance, automated distribution schedules; integrates with fundraising. | Nonprofit-focused, less flexible for individual use; high complexity for small teams. |
| Fundriver                          | Endowment Fund/Nonprofit                | Unitized asset pools for short/long-term allocations, UPMIFA-compliant income schedules; supports bucket-like cashflow planning. | Limited to accounting workflows; no AI-driven insights; requires GL integration. |
| Foundant CommunitySuite            | Endowment Fund/Nonprofit                | Pooled investments allocated for grants (short-term) and growth (long-term); bucket-like income distribution tracking. | Grant-focused; less emphasis on individual tax planning; complex for small orgs. |
| Bank of America Private Bank Endowment Solutions | Endowment Fund/Nonprofit                | Bucket-style allocation (liquidity, income, growth), ESG/impact investing, revenue strategies; governance support. | High AUM requirements; U.S.-centric; less accessible for small nonprofits. |

# Development Process
The system will be developed using a hybrid agile-waterfall process in which chatbot conversations and feedback from rapid prototyping are used to iteratively refine the requirements, generate design artifacts in UML and ERD notation and incrementally deliver functionality. The design artifacts will provide a complete and independent description of the system that is not dependent on chatbot history. 

The development process will follow the phased sequence shown below

1. Refine this requirements specification to ensure it is clear, correct and complete
2. Create and validate a UML Class Diagram 
3. Create and validate UML Sequence Diagrams 
4. Create and validate an ERD Diagram 
5. Define the interface layout and elements 
6. Define the software architecture and components
7. Select the software packages and libraries to be used in development
8. Identify the main functional components and their associated features
9. Define an implementation plan based on Use-Case delivery
10. Incrementally implement and test the features, functions and use-cases

Each phase will include unit tests, integration tests, and user acceptance testing to validate functionality and phases will be repeated when refinements identified in subsequent phases necessitate changes.

**Current Task:**
Phase 1: Refine this requirements specification document - the chatbot will use its domain knowledge to perform a detailed analysis of the entire document and then lead a conversation that reviews and refines each section in turn. 

## Environment

The system will be developed in a VS Code environment on a Windows 11 PC. When completed it will be put into production on an Ubuntu server (version 24.04) which is accessible only to users on a private home network. In the future it will be migrated to a cloud platform, such as AWS, and run as a commercial service for paying users, each of whom will have a single portfolio. 

Git will be used for the version control of all documentation and code. The repository will contain the following design artifacts in a folder named -Design
- Requirements.md (this document)
- Classes.mmd (UML Class diagram)
- Components.mmd (UML Component diagram)
- Seq-xxx (UML Sequence diagrams where "xxx" is the name of the sequence)
- ERD.mmd (Entity Relationship Design in mermaid format)

The repository is publicly accessible from https://github.com/SRKershaw/Bouquet

## Chatbot Instructions

The chatbot should follow these instructions in all conversations - they represent an implicit prompt that prefixes every utterance I make
 
- The chatbot should act as a constructively critical collaborator that has deep knowledge of finance, investing, retirement planning, UK tax law, software design, user interfaces and Python coding
- The chatbot should challenge any requirements that appear to be wrong
- The chatbot should question any requirements that are unclear, ambiguous or incomplete
- The chatbot should suggest corrections, improvements and enhancements by using critical thinking and surveys of best practice
- The chatbot dialogue should focus on one topic at a time to ensure clarity of communications. 
- When a topic has been dealt with to our mutual satisfaction, the chatbot must provide an updated version of this document, the associated design artifacts and Python files to ensure they contain all the necessary details and there is no dependence on dialogue history. The chatbot will then suggest options for the next topic.
- These instructions represent an implicit prompt for each response I make
 
# Definitions
1. An Asset is a transactable instrument which is classified as either cash, securities (including bonds, equities, and pooled investment vehicles), or property
2. A Unit is the transactable component of an asset (eg £1, a share or a bond)
3. An Account holds Units
4. A Bank Account (Current & Deposit) holds Cash
5. A Brokerage Account (Trading, ISA, SIPP) holds Cash and Securities
6. A notional Property Account holds Property 
7. An Institution (eg a bank or brokerage) provides Accounts
8. A Bucket is a notional entity that is created by the user and is assigned Units
9. A Portfolio consists of all Units owned by the User
10. A Position is the value of all Units of a specific Asset that are in the Portfolio.
11. A Holding is the value of all Units of a specific Asset that are held in a specific Account or have been assigned to a specific Bucket
12. An Income pipeline consists of a series of scheduled releases (Releases) of Assets in Bucket 2 that have been liquidated to provide income for Bucket 1.
13. A Release is a proportion of an asset with a specified release date that determines when the cash resulting from its liquidation is transferred to Bucket 1
14. A Budget is notional entity created by the user to define a target amount of spending on a specified category (eg "Everyday", "Major", "Emergency") over a specified time period 
15. A Transaction causes a change in the number of Units held in an Account, Bucket or Budget (eg a Purchase or Sale of Units, a Credit, Debit or Transfer)
16. A Watchlist is a group of assets that can be analysed

# Requirements Summary
## Functional Summary
1. The system manages a single Portfolio of Assets that are owned by one or more individuals and held in one or more accounts at one or more institutions.
2. The system enables users to record transactions performed on Units
3. The system enables users to monitor, analyse, forecast and model the performance of the Portfolio, Assets, Positions, Holdings, Buckets, Budgets and Tax
4. The system enables user to define rules and objectives that generate alerts and guide actions.
5. The system accesses online real-time and historic financial data (eg via yfinance)
6. The system may access online banking data in the future

## Non-Functional Summary
1. The system is for personal use by financially literate and tech-savvy UK residents
2. The base currency is GBP and UK tax rules will be applied. Some consideration should be given to ensuring it can easily be adapted for use by residents in other jurisdictions (eg USA)
3. The backend will be coded in Python and use an SQLite database for persistent storage. 
4. The frontend will provide an HTML based interface which will accessed from a browser and it will be coded in *TBD*
5. The system will be modular and extensible with a minimum of edge cases to ease the addition of new functionality.

# Use Cases

This section outlines the high-level use-cases supported by the system. Each use-case references external and internal actors, where internal actors represent logical system responsibilities rather than specific architectural components. These references are intended to clarify behavior, not prescribe implementation.

## Configure and Authenticate User
**Description**: Configure users (e.g., retirees, co-owners) for portfolio access, authenticate existing users, or create the Primary User during initial setup.  
**Actors**: User (Primary User or co-owner), System.  
**Preconditions**: None for initial Primary User setup; user account exists for authentication or configuration.  
**Basic Flow**:  
- User selects the option to log in, configure users, or initialize the system.  
- System determines the scenario based on user input and system state.  
**Alternative Flow A: Initial Setup (No Users Exist)**:  
- User enters Primary User details (e.g., name, authentication credentials) and assigns administrator role.  
- System validates inputs (e.g., unique credentials).  
- System saves Primary User with administrator permissions.  
**Alternative Flow B: Authenticate Existing User**:  
- User enters authentication credentials (e.g., username, password).  
- System validates credentials.  
- System authenticates the user.  
**Alternative Flow C: Configure New User**:  
- Authenticated Primary User enters new user details (e.g., name, credentials) and assigns roles (e.g., view-only, edit access to portfolio).  
- System validates inputs (e.g., unique credentials).  
- System saves new user configurations.  
**Postconditions**: User is authenticated and can access the system; new users are configured with specified permissions; Primary User is created on initial setup.

## Configure an Institution
**Description**: Configure an institution (e.g., bank, broker) for accounts.  
**Actors**: User, System.  
**Preconditions**: User is authenticated.  
**Basic Flow**:  
- User selects the option to configure an institution.  
- User enters institution details (e.g., name, type like bank or broker).  
- System validates inputs (e.g., non-empty name).  
- System saves the institution.  
**Postconditions**: Institution is available for associating with accounts.

## Configure an Account
**Description**: Configure an account (e.g., bank, brokerage) to hold assets.  
**Actors**: User, System.  
**Preconditions**: Institution is configured; user is authenticated.  
**Basic Flow**:  
- User selects the option to configure an account.  
- User specifies account details (e.g., type, institution, account number).  
- System validates inputs (e.g., valid institution).  
- System saves the account.  
**Postconditions**: Account is ready for holding assets and transactions.

## Configure a Bucket
**Description**: Configure a bucket (1-4) by selecting asset positions and specifying proportions (percentage or units) to assign, with the system automatically distributing across accounts and tracking originating accounts for tax implications. For Bucket 2, optionally configure an income pipeline (a series of scheduled cash releases to Bucket 1) with releases (cash amounts with release dates from liquidated or planned-to-be-liquidated assets). For Bucket 4, optionally configure property details.  
**Actors**: User (Primary User or co-owner with edit permissions), System.  
**Preconditions**: Accounts and asset positions exist; user is authenticated.  
**Basic Flow**:  
- User selects a bucket (1, 2, 3, or 4).  
- User selects asset positions (e.g., total shares of a stock) from the portfolio.  
- User specifies the total proportion of each position to assign to the bucket (e.g., 50% or 100 shares).  
- System automatically distributes the proportion across accounts holding the asset (e.g., proportional to account holdings or prioritizing tax-advantaged accounts), displaying the proposed account breakdown for confirmation or adjustment.  
- For Bucket 2 (optional): User configures an income pipeline by specifying a cash amount and a release date for each release, referencing a liquidated or planned-to-be-liquidated asset; for bonds, the system validates that the release date is on or after the liquidation date, which must be at or after the bond’s maturity date.  
- For Bucket 4 (optional): User enters property valuation and rental income manually.  
- System validates inputs (e.g., positions exist, proportions do not exceed position totals, accounts are valid, bond release dates are valid relative to liquidation/maturity dates).  
- System saves the bucket configuration, linking each holding’s proportion to its originating account and each release to its liquidation source for tax purposes, and updates the cashflow forecast.  
**Postconditions**: Bucket is configured with account-linked holdings, integrated into cashflow, and ready for tax-aware analysis or transactions; Bucket 2 includes income pipeline releases if configured, linked to liquidation sources.

## Configure a Budget
**Description**: Create a budget for spending categories (recurring or itemised) with budgeted items, ensuring sufficient cash in Bucket 1 for current-year spending and Bucket 2 releases for future years.  
**Actors**: User, System.  
**Preconditions**: User is authenticated.  
**Basic Flow**:  
- User selects the option to create a budget.  
- User specifies the budget category (e.g., Everyday, Major Purchases) and type (recurrent or itemised).  
- User enters details (e.g., annual amount and inflation adjustment for recurrent, cost and date range for itemised).  
- User adds budgeted items (e.g., "Groceries" as recurring, "New Car" as non-recurring), specifying name, amount, start/end dates (mandatory), and optional inflation rate override (defaults to system rate).  
- System validates inputs (e.g., positive amounts, valid dates).  
- System saves the budget, including budgeted items, and updates the cashflow forecast with inflation-adjusted projections.  
**Postconditions**: Budget is tracked against spending, with items ensuring liability coverage in Bucket 1 and future releases.

## Add an Asset to Catalogue
**Description**: Add an asset (e.g., security, property, cash) to the portfolio’s catalogue for use in transactions and bucket configurations.  
**Actors**: User (Primary User or co-owner with edit permissions), System.  
**Preconditions**: User is authenticated.  
**Basic Flow**:  
- User selects the option to add an asset.  
- User enters asset details (e.g., type, name, ticker for securities, valuation for property, maturity date for bonds).  
- System validates inputs (e.g., unique ticker for securities using external market data, positive valuation for property, valid maturity date for bonds).  
- System adds the asset to the catalogue.  
**Postconditions**: Asset is available for transactions and bucket configurations.

## Create a Transaction
**Description**: Record a transaction (e.g., purchase, sale, income) with double-entry, associating dividends/coupons with releases if applicable.  
**Actors**: User, System.  
**Preconditions**: Accounts and assets exist; user is authenticated.  
**Basic Flow**:  
- User selects the option to create a transaction.  
- User specifies transaction type (e.g., purchase, sale, transfer).  
- User enters details (e.g., date, amount, asset, account, bucket, fees, taxes, ReleaseId for dividends/coupons).  
- System validates inputs (e.g., sufficient funds, no negative balances, ownership consistency, FX rates).  
- System records the transaction using a double-entry structure, updates balances/logs, and auto-associates dividends/coupons to Release via asset match if ReleaseId provided.  
- System logs the transaction with a unique identifier and versioned history.  
**Postconditions**: Portfolio is updated with audit trail; allocations refreshed.

## Manage Bucket Assignments
**Description**: Assign, unassign, or transfer units between buckets.  
**Actors**: User, System.  
**Preconditions**: Buckets and assets exist; user is authenticated.  
**Basic Flow**:  
- User selects action (assign, unassign, or transfer) and specifies source/destination buckets and units.  
- System validates inputs (e.g., sufficient units).  
- System updates holdings as a transaction with double-entry structure.  
- System logs the transaction.  
**Postconditions**: Buckets reflect new assignments.

## View Holdings and Positions
**Description**: List portfolio positions or holdings with flexible scope (e.g., portfolio summary, account holdings, bucket holdings, account-linked bucket holdings for tax implications).  
**Actors**: User (Primary User or co-owner with view permissions), System.  
**Preconditions**: Portfolio has data; user is authenticated.  
**Basic Flow**:  
- User selects the option to view holdings or positions.  
- User specifies the scope (e.g., all portfolio positions, holdings in an account, holdings in a bucket, bucket holdings linked to a specific account for tax purposes).  
- System retrieves data from transaction history and market data (e.g., yfinance), including account origins for bucket holdings.  
- System displays results (e.g., asset, quantity, value, originating account for tax implications).  
**Postconditions**: User views the requested portfolio information, including tax-relevant data.

## Define Guardrails
**Description**: Set thresholds for alerts (e.g., cash shortfalls, replenishment needs).  
**Actors**: User, System.  
**Preconditions**: Buckets and budgets are configured; user is authenticated.  
**Basic Flow**:  
- User selects the option to define guardrails.  
- User defines rules (e.g., Bucket 1 below £5,000 triggers red alert).  
- System validates inputs (e.g., valid thresholds).  
- System saves guardrails.  
**Postconditions**: Guardrails are active for monitoring.

## Project External Income
**Description**: Add projections for income (e.g., pension, rental), linked to users for tax purposes.  
**Actors**: User, System.  
**Preconditions**: User is authenticated.  
**Basic Flow**:  
- User selects the option to add external income.  
- User enters income type, amount, dates, optional inflation adjustment, and links to users for tax.  
- System validates inputs (e.g., positive amounts).  
- System saves projection and updates cashflow forecast.  
**Postconditions**: Income is integrated into cashflow, linked to users.

## Generate Cashflow Forecast
**Description**: Calculate rolling forecast from buckets, budgets, and income, applying portfolio rules (e.g., SIPP drawdown placeholder).  
**Actors**: User, System.  
**Preconditions**: Data is configured; user is authenticated.  
**Basic Flow**:  
- User requests a cashflow forecast for a period.  
- System computes forecast using Bucket 1 cash, inflation-adjusted budget items, and income.  
- System applies portfolio rules (e.g., SIPP drawdown placeholder for tax).  
- System displays results (e.g., shortfalls, pipeline needs).  
**Postconditions**: Forecast is available for decisions.

## Generate and View Alerts
**Description**: System generates alerts based on guardrails.  
**Actors**: User, System.  
**Preconditions**: Guardrails are defined; user is authenticated.  
**Basic Flow**:  
- System monitors buckets, budgets, and assets against guardrails.  
- System detects a condition (e.g., Bucket 1 shortfall).  
- System generates an alert (green, amber, red, purple) with details.  
- User views and acknowledges the alert.  
**Postconditions**: Alert is logged.

## Run Portfolio Analysis
**Description**: Analyze performance (returns, risks, tax; with potential AI).  
**Actors**: User, System.  
**Preconditions**: Transactions and market data exist; user is authenticated.  
**Basic Flow**:  
- User selects analysis type (e.g., returns, risk, tax estimate).  
- System retrieves data (e.g., yfinance) and computes metrics.  
- System displays results.  
**Postconditions**: Analysis informs planning.

## Run Scenario Tests
**Description**: Model alternatives (e.g., stress tests, strategies) using Scenario portfolios.  
**Actors**: User, System.  
**Preconditions**: Active portfolio exists; user is authenticated.  
**Basic Flow**:  
- User selects the Active portfolio and baseDate for snapshot.  
- System creates Scenario portfolio, cloning aggregated account categories (by owner/type) and bucket assignments at baseDate, setting snapshotValue.  
- User modifies Scenario portfolio (e.g., adjust allocations, rules).  
- System normalizes balances to user-selected comparison date using inflation/growth rates.  
- System runs simulation and displays outcomes.  
**Postconditions**: User evaluates scenarios, with comparable reports to Active portfolio.

# Class Definitions

## Portfolio
**Description**: Represents a portfolio containing aggregated accounts, buckets, rules, and parameters for planning and scenario modeling. An "Active" portfolio reflects the current status of the user's investments and "Scenario" portfolios clone accounts and buckets at a `baseDate` for comparability, with no transaction history.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | portfolioId           | String            | Unique identifier                             |
| +          | name                  | String            | Non-empty, e.g., "Family Portfolio", "Scenario 1" |
| +          | type                  | Enum              | Values: Active, Scenario                           |
| +          | accounts              | List<Account>     | Active portfolio accounts; empty for scenarios     |
| +          | accountCategories     | Map<User, AccountType, Decimal> | Aggregated balances by owner and type for scenarios |
| +          | buckets               | List<Bucket>      | Cloned assignments, modifiable in Scenarios      |
| +          | rules                 | List<Rule>        | Guardrails, parameters (e.g., inflation rate) |
| +          | isVersioned           | Boolean           | True for Scenarios, enables save/compare        |
| +          | baseDate              | Date              | Snapshot date for Scenario portfolio balances    |
| +          | snapshotValue         | Decimal           | Total portfolio value at baseDate (GBP)       |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | createScenario     | sourcePortfolio: Portfolio, baseDate: Date | Portfolio   | Clones Active portfolio, aggregates accounts/buckets at baseDate             |
| +          | compare         | otherPortfolio: Portfolio, compareDate: Date | ComparisonReport | Compares forecasts, normalizing to compareDate using inflation/growth |
| +          | normalizeToDate | targetDate: Date                   | Decimal     | Adjusts snapshotValue to target date using Settings.inflationRate           |

**Relationships**:
- **Composition**: Contains `Account`, `Bucket`, `Rule`.
- **Association**: Dummies reference Active portfolio as source.

## Asset
**Description**: Represents a financial asset (e.g., cash, bond, equity, fund, property) in the portfolio, stored in the Catalogue, held in Accounts, and assigned to Buckets. Base class for specific asset types.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | assetId               | String            | Unique identifier                             |
| +          | name                  | String            | Non-empty freeform text                       |
| +          | class                 | Enum              | Values: Cash, Bond, Equity, Fund, Property    |
| +          | yfinanceTicker        | String            | Nullable, $$CASH for cash assets              |
| +          | isin                  | String            | Nullable, unique if provided                  |
| +          | marketIdentifier      | String            | Nullable, e.g., XLON, XNYS, XNAS, XPAR        |
| +          | website               | String            | Nullable, URL for asset details               |
| +          | country               | String            | Nullable, country of origin                   |
| +          | localCurrency         | Enum              | Values: GBP (default), USD, EUR, etc.         |
| +          | currentPriceLocal     | Decimal           | Non-negative, price in local currency         |
| +          | currentPriceUKP       | Decimal           | Non-negative, price in GBP, derived           |
| +          | priceVolumeHistory    | TimeSeries        | Nullable, historical price/volume data        |
| +          | volatility            | Decimal           | Nullable, calculated volatility metric        |
| -          | owned                 | Boolean           | True if purchased, default false              |
| -          | active                | Boolean           | True if units currently owned, default false  |

**Operations**:
| Visibility | Name            | Parameters                                              | Return Type | Description                                                                 |
|------------|-----------------|--------------------------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | create          | name: String, class: Enum, ... (other attributes)       | Asset       | Adds asset to Catalogue, validates attributes (e.g., ticker via yfinance)   |
| +          | edit            | name: String, class: Enum, ... (other attributes)       | Void        | Updates attributes, validates changes (e.g., ticker uniqueness)            |
| +          | view            | None                                                   | Asset       | Returns asset’s attributes for display                                      |
| +          | delete          | None                                                   | Void        | Removes asset if never purchased (owned = false)                            |
| +          | analysis        | None                                                   | AnalysisData| Returns metrics (e.g., charts, CAGR, RTM, momentum)                        |
| +          | position        | None                                                   | Position    | Returns total units and value across portfolio                             |
| +          | holdings        | filter: FilterCriteria, sort: SortCriteria              | List<Holding> | Returns filtered/sorted holdings (by account, bucket, etc.)                |
| +          | transactions    | filter: FilterCriteria, sort: SortCriteria              | List<Transaction> | Returns filtered/sorted transaction history (e.g., buy, sell, move)        |

**Relationships**:
- **Generalization**: Base class for `Cash`, `Bond`, `Equity`, `Fund`, `Property`.
- **Association**: Stored in `Catalogue` (many-to-one), held in `Account` as `Holding` (many-to-many), assigned to `Bucket` (many-to-many), tracked in allocation tables.

## Cash
**Description**: Represents a cash asset, with units as currency amounts (e.g., £1, 1 USD). Inherits from `Asset`.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| Inherited from `Asset`, no additional attributes | | | |

**Operations**: Inherited from `Asset`.

**Relationships**:
- **Generalization**: Inherits from `Asset`.
- **Association**: Assigned to Bucket 1 automatically, held in `Account` (type: Current, Deposit).

## Bond
**Description**: Represents a bond asset, with units as individual bonds. Inherits from `Asset`.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | issuer                | String            | Non-empty, bond issuer name                   |
| +          | issueDate             | Date              | Non-null, date of bond issuance               |
| +          | maturityDate          | Date              | Non-null, date of bond maturity               |
| +          | couponRate            | Decimal           | Non-negative, annual coupon rate              |
| +          | couponFrequency       | Enum              | Values: Monthly, Quarterly, Semi-Annual, Annual |

**Operations**: Inherited from `Asset`.

**Relationships**:
- **Generalization**: Inherits from `Asset`.
- **Association**: Held in `Account` (type: Trading, ISA, SIPP), assigned to Bucket 2/3, linked to `Release` for income pipelineing.

## Equity
**Description**: Represents an equity (share) asset, with units as shares. Inherits from `Asset`.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | sector                | String            | Nullable, e.g., Technology, Energy, Durables  |
| +          | dividendFrequency     | Enum              | Nullable, e.g., Monthly, Quarterly, Annual     |

**Operations**: Inherited from `Asset`.

**Relationships**:
- **Generalization**: Inherits from `Asset`.
- **Association**: Held in `Account` (type: Trading, ISA, SIPP), assigned to Bucket 2/3.

## Fund
**Description**: Represents a fund asset (e.g., ETF, Unit Trust), with units as shares. Inherits from `Asset`.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | issuer                | String            | Non-empty, fund issuer name                   |
| +          | type                  | Enum              | Values: ETF, Unit Trust, Investment Trust     |
| +          | style                 | String            | Nullable, e.g., Index, Bond, Growth, Value    |
| +          | sector                | String            | Nullable, e.g., Technology, Energy, Durables  |
| +          | ter                   | Decimal           | Non-negative, Total Expense Ratio             |

**Operations**: Inherited from `Asset`.

**Relationships**:
- **Generalization**: Inherits from `Asset`.
- **Association**: Held in `Account` (type: Trading, ISA, SIPP), assigned to Bucket 2/3.

## Property
**Description**: Represents a property asset, with units as individual properties. Inherits from `Asset`.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| Inherited from `Asset`, no additional attributes | | | |

**Operations**: Inherited from `Asset`.

**Relationships**:
- **Generalization**: Inherits from `Asset`.
- **Association**: Held in `Account` (type: Property), assigned to Bucket 4 automatically.

## Catalogue
**Description**: Stores all assets available in the system for portfolio management.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | assets                | List<Asset>       | Collection of all assets in the system        |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | addAsset        | asset: Asset                       | Void        | Adds an asset to the catalogue, validates uniqueness                        |
| +          | removeAsset     | assetId: String                    | Void        | Removes an asset if not owned                                              |
| +          | getAsset        | assetId: String                    | Asset       | Retrieves an asset by ID                                                  |
| +          | listAssets      | filter: FilterCriteria, sort: SortCriteria | List<Asset> | Returns filtered/sorted list of assets                              |

**Relationships**:
- **Association**: Contains many `Asset` instances (one-to-many).

## Account
**Description**: Represents a financial account (e.g., ISA, brokerage, notional property account) holding asset units as holdings.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | accountId             | String            | Unique identifier                             |
| +          | name                  | String            | Non-empty, free text                          |
| +          | owners                | List<User>        | Non-empty, one or more users                  |
| +          | number                | String            | Nullable, account number                      |
| +          | institution           | Institution       | Nullable, providing institution               |
| +          | type                  | Enum              | Values: Current, Deposit, Trading, ISA, SIPP, Property |
| +          | currency              | Enum              | Values: GBP (default), USD, EUR, etc.         |
| +          | currencyBalance       | Decimal           | Non-negative, balance in account currency if not GBP |
| +          | balance               | Decimal           | Non-negative, balance in GBP                  |
| +          | status                | Enum              | Values: Active, Inactive (no assets held)     |
| +          | holdings              | List<Holding>     | Collection of asset holdings in the account   |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | create          | name: String, type: Enum, ...      | Account     | Creates an account, associates with institution                             |
| +          | edit            | name: String, type: Enum, ...      | Void        | Updates account attributes                                                 |
| +          | delete          | None                               | Void        | Deletes account if never held assets                                       |
| +          | balance         | None                               | Decimal     | Returns current value of all assets in account                             |
| +          | holdings        | filter: FilterCriteria, sort: SortCriteria | List<Holding> | Returns filtered/sorted list of holdings (by name, type, value, bucket) |
| +          | transactions    | filter: FilterCriteria, sort: SortCriteria | List<Transaction> | Returns filtered/sorted transaction history (by date, type, account)   |

**Relationships**:
- **Association**: Contains many `Holding` instances (one-to-many), linked to `Asset` via `Holding`, owned by `User` (many-to-many), provided by `Institution` (many-to-one).

## Institution
**Description**: Represents a financial institution (e.g., bank, brokerage) providing accounts.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | institutionId         | String            | Unique identifier                             |
| +          | name                  | String            | Non-empty, free text                          |
| +          | type                  | Enum              | Values: Bank, Brokerage, Property (notional)  |
| +          | balance               | Decimal           | Non-negative, total value of accounts in GBP  |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | create          | name: String, type: Enum           | Institution | Creates a new institution                                                  |
| +          | edit            | name: String, type: Enum           | Void        | Updates institution attributes                                             |
| +          | delete          | None                               | Void        | Deletes institution if no accounts held assets                             |
| +          | balance         | None                               | Decimal     | Returns total value of all accounts at institution                         |
| +          | accounts        | filter: FilterCriteria, sort: SortCriteria | List<Account> | Returns filtered/sorted list of accounts (by name, owner, type, value) |

**Relationships**:
- **Association**: Provides many `Account` instances (one-to-many).

## Bucket
**Description**: Represents a notional pool (1-4) for asset allocation to generate retirement income.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | bucketId              | String            | Unique identifier, values: 1, 2, 3, 4         |
| +          | name                  | String            | Non-empty, defaults: Short, Medium, Long, Property |
| +          | balance               | Decimal           | Non-negative, total value of holdings in GBP  |
| +          | holdings              | List<Holding>     | Collection of asset holdings in the bucket    |
| +          | Releases                 | List<Release>        | Bucket 2 only, income pipeline Releases            |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | edit            | name: String                       | Void        | Updates bucket name                                                       |
| +          | balance         | None                               | Decimal     | Returns current value of all assets in bucket                              |
| +          | holdings        | filter: FilterCriteria, sort: SortCriteria | List<Holding> | Returns filtered/sorted list of holdings (by name, type, value, account) |
| +          | transactions    | filter: FilterCriteria, sort: SortCriteria | List<Transaction> | Returns filtered/sorted transaction history (by date, type, account)   |
| +          | addHolding      | holding: Holding                   | Void        | Assigns asset units to the bucket                                          |
| +          | addRelease         | Release: Release                         | Void        | Adds a Release to Bucket 2’s income pipeline, validates release date            |

**Relationships**:
- **Association**: Contains many `Holding` instances (one-to-many), linked to `Asset` via `Holding`. Contains many `Release` instances for Bucket 2 (one-to-many).

## Release
**Description**: Represents a scheduled cash release event in Bucket 2’s income pipeline, linked to a liquidation transaction.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | ReleaseId                | String            | Unique identifier, auto-generated             |
| +          | cashAmount            | Decimal           | Non-negative, amount to release to Bucket 1   |
| +          | releaseDate           | Date              | Non-null, date of cash release, after liquidation |
| +          | status                | Enum              | Values: Disposed, Undisposed                  |
| +          | liquidationSource     | Transaction       | Nullable, linked liquidation transaction       |
| +          | assets                | List<Asset>       | Non-empty, assets providing the cash          |
| +          | accounts              | List<Account>     | Non-empty, accounts for tax tracking          |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | create          | cashAmount: Decimal, releaseDate: Date, ... | Void        | Creates a Release, validates release date (e.g., after bond maturity)         |
| +          | assign          | asset: Asset, units: Decimal, account: Account | Void        | Assigns asset units to the Release, ensures unique assignment                 |
| +          | balance         | None                               | Decimal     | Returns total value of the Release                                           |
| +          | assets          | filter: FilterCriteria, sort: SortCriteria | List<Asset> | Returns filtered/sorted list of assets in the Release                        |
| +          | associateTransaction | transaction: Transaction           | Void        | Auto-links dividend/coupon transaction to Release via asset match             |

**Relationships**:
- **Association**: Belongs to `Bucket` (many-to-one), linked to `Asset` and `Account`, references `Transaction` (many-to-one for dividends/coupons).

## Holding
**Description**: Represents units of an asset held in an account or assigned to a bucket.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | holdingId             | String            | Unique identifier                             |
| +          | asset                 | Asset             | Non-null, referenced asset                    |
| +          | account               | Account           | Non-null, owning account                      |
| +          | bucket                | Bucket            | Nullable, assigned bucket if any               |
| +          | disposalDate          | Date              | Nullable, linked to Release’s release date       |
| +          | units                 | Decimal           | Non-negative, number of units held            |
| +          | costBasis             | Decimal           | Non-negative, acquisition cost in GBP         |
| +          | totalIncomeReceived   | Decimal           | Non-negative, income from interest/dividends/rent |
| +          | totalReturnGBP        | Decimal           | Total return in GBP                           |
| +          | totalReturnPercent    | Decimal           | Total return as percentage                    |
| +          | twr                   | Decimal           | Time-Weighted Return                          |
| +          | mwr                   | Decimal           | Money-Weighted Return                         |
| +          | cagr                  | Decimal           | Compound Annual Growth Rate                   |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | updateUnits     | units: Decimal                     | Void        | Updates holding units, recalculates value and metrics                      |

**Relationships**:
- **Association**: Links `Asset` to `Account` and `Bucket` (many-to-one), tracked in allocation tables.

## Position
**Description**: Represents the total units of an asset held across all accounts in the portfolio.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | positionId            | String            | Unique identifier                             |
| +          | asset                 | Asset             | Non-null, referenced asset                    |
| +          | totalUnits            | Decimal           | Non-negative, total units across accounts     |
| +          | costBasis             | Decimal           | Non-negative, total acquisition cost in GBP   |
| +          | totalIncomeReceived   | Decimal           | Non-negative, income from interest/dividends/rent |
| +          | totalReturnGBP        | Decimal           | Total return in GBP                           |
| +          | totalReturnPercent    | Decimal           | Total return as percentage                    |
| +          | twr                   | Decimal           | Time-Weighted Return                          |
| +          | mwr                   | Decimal           | Money-Weighted Return                         |
| +          | cagr                  | Decimal           | Compound Annual Growth Rate                   |
| +          | status                | Enum              | Values: Active, Inactive                      |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | view            | None                               | Position    | Returns position details for display                                       |

**Relationships**:
- **Association**: Linked to `Asset` (one-to-one), derived from `Holding` instances.

## Budget
**Description**: Represents a spending plan for user-defined categories, ensuring sufficient cash in Bucket 1 and future Releases.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | budgetId              | String            | Unique identifier                             |
| +          | name                  | String            | Non-empty, free text                          |
| +          | type                  | Enum              | Values: Recurrent, Itemised                   |
| +          | startDate             | Date              | Non-null, budget start date                   |
| +          | endDate               | Date              | Non-null, budget end date                     |
| +          | forecastGrowthRate    | Decimal           | Nullable, growth rate for future years        |
| +          | amount                | Decimal           | Non-negative, budgeted amount in GBP          |
| +          | balance               | Decimal           | Non-negative, remaining balance in GBP        |
| +          | category              | String            | Non-empty, e.g., Ordinary, Major, Emergency   |
| +          | items                 | List<BudgetedItem>| Itemized expenses, recurring or non-recurring |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | create          | name: String, type: Enum, ...      | Budget      | Creates a budget with specified attributes                                 |
| +          | edit            | name: String, type: Enum, ...      | Void        | Updates budget attributes                                                  |
| +          | balance         | None                               | Decimal     | Returns current budget balance                                            |
| +          | checkCoverage   | None                               | CoverageReport | Checks Bucket 1 for current year, Bucket 2 Releases for future (inflation-adjusted) |
| +          | addItem         | item: BudgetedItem                 | Void        | Adds an item to the budget                                                |

**Relationships**:
- **Association**: Linked to `Transaction` (many-to-many, for debits), `BudgetAllocation` (cash), `Bucket` (coverage).

## BudgetedItem
**Description**: Represents an item within a budget, marked as recurring (inflation-linked) or non-recurring.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | itemId                | String            | Unique identifier                             |
| +          | budget                | Budget            | Non-null, parent budget                       |
| +          | name                  | String            | Non-empty, e.g., "Groceries", "New Car"       |
| +          | recurring             | Boolean           | True for inflation-linked items               |
| +          | amount                | Decimal           | Non-negative, item amount in GBP              |
| +          | inflationRateOverride | Decimal           | Nullable, defaults to Settings.inflationRate  |
| +          | startDate             | Date              | Non-null, item start date                     |
| +          | endDate               | Date              | Non-null, item end date                       |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | create          | name: String, recurring: Boolean, ... | BudgetedItem | Creates a budget item, applies inflation if recurring                     |
| +          | project         | forecastPeriod: DateRange           | Decimal     | Projects item cost with inflation (if recurring)                           |

**Relationships**:
- **Association**: Belongs to `Budget` (many-to-one).

## Transaction
**Description**: Represents a financial transaction managing asset units, cash balances, and budgets.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | transactionId         | String            | Unique identifier                             |
| +          | date                  | Date              | Non-null, transaction date                    |
| +          | type                  | Enum              | Values: Purchase, Sale, Credit, Debit, Transfer, Interest, Coupon, Dividend, Adjustment, Valuation |
| +          | subCategory           | String            | Nullable, user-defined (e.g., Salary, Tax, Holiday) |
| +          | sourceAccount         | Account           | Nullable, “External” for credits/debits       |
| +          | destinationAccount    | Account           | Nullable, “External” for credits/debits       |
| +          | bucket                | Bucket            | Nullable, associated bucket                   |
| +          | asset                 | Asset             | Nullable, for Purchase, Sale, Coupon, Dividend |
| +          | ReleaseId                | String            | Nullable, for Coupon/Dividend association to Release |
| +          | units                 | Decimal           | Nullable, number of asset units               |
| +          | unitPrice             | Decimal           | Nullable, price per unit in unit currency     |
| +          | unitCurrency          | Enum              | Default: GBP, Values: GBP, USD, EUR, etc.     |
| +          | exchangeRate          | Decimal           | Default: 1 for GBP, non-negative              |
| +          | transactionFee        | Decimal           | Non-negative, fee in GBP                      |
| +          | amount                | Decimal           | Derived: units * unitPrice * exchangeRate - transactionFee |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | create          | date: Date, type: Enum, ReleaseId: String (optional for Coupon/Dividend) | Transaction | Creates transaction, auto-associates to Release via asset match if ReleaseId provided |
| +          | validate        | None                               | Boolean     | Checks transaction rules (e.g., units held, ownership, tax rules)          |

**Relationships**:
- **Association**: Linked to `Account` (source/destination), `Asset`, `Bucket`, `Budget`, `Release`.

## User
**Description**: Represents an individual who owns or shares accounts.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | userId                | String            | Unique identifier                             |
| +          | name                  | String            | Non-empty, full name                          |
| +          | dateOfBirth           | Date              | Non-null, user’s date of birth                |
| +          | email                 | String            | Non-empty, valid email address                |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | create          | name: String, dateOfBirth: Date, email: String | User | Creates a user                                                        |
| +          | edit            | name: String, dateOfBirth: Date, email: String | Void | Updates user attributes                                               |

**Relationships**:
- **Association**: Owns many `Account` instances (many-to-many), linked to `Income` for tax.

## Income
**Description**: Represents projected or actual income sources for cashflow forecasting.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | incomeId              | String            | Unique identifier                             |
| +          | name                  | String            | Non-empty, e.g., "State Pension"              |
| +          | sourceType            | Enum              | Values: Pension, Rental, Annuity, Salary, etc. |
| +          | users                 | List<User>        | Non-empty, for tax purposes                   |
| +          | startDate             | Date              | Non-null, e.g., age 67 for pension            |
| +          | endDate               | Date              | Nullable, for finite streams                  |
| +          | amount                | Decimal           | Non-negative, annual amount in GBP            |
| +          | isInflationAdjusted   | Boolean           | True if grows with inflation                  |
| +          | adjustmentRate        | Decimal           | Nullable, e.g., CPI for pension               |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | create          | name: String, sourceType: Enum, ... | Income      | Creates income projection for forecasting                                 |
| +          | project         | forecastPeriod: DateRange           | List<ProjectedIncome> | Generates inflation-adjusted projections for future years                  |

**Relationships**:
- **Association**: Linked to `User` (many-to-many), `Transaction` (actuals), `Budget` (coverage), `Settings` (inflation rate).

## Taxation
**Description**: Placeholder for tax rules applied to transactions based on account type (e.g., SIPP drawdown).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | taxationId            | String            | Unique identifier                             |
| +          | accountType           | Enum              | Values: Current, Deposit, Trading, ISA, SIPP, Property |
| +          | taxRules              | String            | Description of tax rules (TBD)                |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | applyRules      | transaction: Transaction            | Decimal     | Applies tax rules to calculate tax liability                               |

**Relationships**:
- **Association**: Linked to `Account` (one-to-many), applied to `Transaction`.

## Settings
**Description**: Stores system-wide configuration parameters (singleton).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | accountingBasis       | Enum              | Values: Calendar Year, Tax Year               |
| +          | forecastInflationRate | Decimal           | Non-negative, annual inflation rate           |
| +          | forecastPensionGrowthRate | Decimal       | Non-negative, state pension growth rate       |
| +          | forecastTaxParameters | String            | TBD, tax-related parameters                   |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | update          | accountingBasis: Enum, ...         | Void        | Updates system settings                                                   |

**Relationships**:
- **Dependency**: Used by `Budget`, `Income`, `Portfolio` for forecasting and tax calculations.

## Rule
**Description**: Represents portfolio rules/guardrails, including placeholders for SIPP crystallization/drawdown.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | ruleId                | String            | Unique identifier                             |
| +          | type                  | Enum              | Values: SIPP_Crystallization, Drawdown, Guardrail, etc. |
| +          | parameters            | Map<String, Object> | Rule-specific settings (e.g., drawdown rate) |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | apply           | portfolio: Portfolio                | Void        | Applies rule to portfolio projections                                     |

**Relationships**:
- **Association**: Belongs to `Portfolio` (many-to-one).

## AccountAllocation
**Description**: Tracks asset units allocated to accounts.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | allocationId          | String            | Unique identifier                             |
| +          | asset                 | Asset             | Non-null, referenced asset                    |
| +          | account               | Account           | Non-null, referenced account                  |
| +          | units                 | Decimal           | Non-negative, units allocated                 |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | updateFromTransaction | transaction: Transaction | Void        | Updates allocation after transaction, triggered automatically              |

**Relationships**:
- **Association**: Links `Asset` to `Account` (many-to-one).

## BucketAllocation
**Description**: Tracks asset units allocated to buckets.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | allocationId          | String            | Unique identifier                             |
| +          | asset                 | Asset             | Non-null, referenced asset                    |
| +          | bucket                | Bucket            | Non-null, referenced bucket                   |
| +          | units                 | Decimal           | Non-negative, units allocated                 |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | updateFromTransaction | transaction: Transaction | Void        | Updates allocation after transaction, triggered automatically              |

**Relationships**:
- **Association**: Links `Asset` to `Bucket` (many-to-one).

## ReleaseAllocation
**Description**: Tracks asset units allocated to Releases in Bucket 2.

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | allocationId          | String            | Unique identifier                             |
| +          | asset                 | Asset             | Non-null, referenced asset                    |
| +          | Release                  | Release              | Non-null, referenced Release                     |
| +          | units                 | Decimal           | Non-negative, units allocated                 |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | updateFromTransaction | transaction: Transaction | Void        | Updates allocation after transaction, triggered automatically              |

**Relationships**:
- **Association**: Links `Asset` to `Release` (many-to-one).

## BudgetAllocation
**Description**: Tracks cash units allocated to budgets (Bucket 1 only).

**Attributes**:
| Visibility | Name                  | Type              | Constraints/Notes                              |
|------------|-----------------------|-------------------|-----------------------------------------------|
| +          | allocationId          | String            | Unique identifier                             |
| +          | asset                 | Asset             | Non-null, cash asset only                     |
| +          | budget                | Budget            | Non-null, referenced budget                   |
| +          | units                 | Decimal           | Non-negative, cash units allocated            |
| +          | liability             | Decimal           | Non-negative, remaining budget liability      |

**Operations**:
| Visibility | Name            | Parameters                          | Return Type | Description                                                                 |
|------------|-----------------|------------------------------------|-------------|-----------------------------------------------------------------------------|
| +          | updateFromTransaction | transaction: Transaction | Void        | Updates allocation after transaction, triggered automatically              |

**Relationships**:
- **Association**: Links `Asset` to `Budget` (many-to-one).
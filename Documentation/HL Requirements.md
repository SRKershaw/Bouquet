# Retirement Portfolio Management System - High-Level Requirements
=====================================================================
# Introduction
This document specifies the high-level requirements for a retirement portfolio management system. 

The System provides a suite of integrated tools that supports a systematic financial planning strategy for managing a retirement investment portfolio to generate income and preserve capital. 

The document is intended for an AI chatbot to assist in the specification, design and implementation of the system. It provides high-level requirements which will be refined into a set of detailed use-cases as part of an iterative and phased development process.  

# Background

Retirees face the challenge of generating a reliable and sufficient income stream from their investment portfolio whilst preserving capital for inheritance. This process is complex, stressful and prone to errors that can lead to cash shortfalls. Although several applications support independent tasks such as rebalancing, budgeting and forecasting, none provide a unified solution for individual investors to systematically manage all aspects of financial planning over their retirement.
  
This system fills that gap by providing an integrated set of features built around a rules-based strategy that enable retirees to manage, monitor and analyse their finances with ease. 

The system is intended to meet the needs of 60-75-year-old British retirees with £100k+ portfolios who are financially literate and possess moderate IT skills. It will be offered as a tiered subscription service in which the core functionality will be provided free of charge and additional features will be available for recurring fees.

# Overview

The system is designed around a rules-based hybrid Bucket and Liability-Driven Investment (LDI) strategy. The User's assets are assigned to time-segmented 'Buckets' whose size and composition are determined by forecast cash-flow, portfolio performance and risk appetite.

The system uses four buckets:
- **Bucket 1** holds short-term cash for spending.
- **Bucket 2** holds medium-term, low-volatility assets that provide a pipeline of scheduled income.
- **Bucket 3** holds long-term, volatile assets to generate growth and income.
- **Bucket 4** holds property to generate growth and income

The growth generated from assets in Bucket 3 is used to purchase assets for Bucket 2 which provide a scheduled reliable flow of income for Bucket 1. Bucket 3 and 4 may also be used to provide income for Bucket 1.
  
Cash in Bucket 1 is monitored against projected income and budgeted expenditure and alerts are generated when user-defined cashflow rules are breached so that remedial action can be taken (eg reducing/postponing spending or liquidating assets in other Buckets to provide  cash).

Assets in Bucket 2 provide an income pipeline consisting of a flow of scheduled release events whose frequency size, and horizon are user-configurable. Each release is intended to transfer sufficient cash into Bucket 1 to support the forecast cashflow demands until the next scheduled release. The number of releases determines how many years of spending are supported by the pipeline. Assets not in the flow (i.e. those with no release date) represent a buffer that can be liquidated and transferred to Bucket 1 on an ad-hoc basis (e.g., for interim shortfalls). Each release may contain a combination of:
- Bonds, whose maturity date determines the release date.  
- Other securities with user-defined release dates.
- Scheduled dividends or interest payments.

Assets in Bucket 3 generate growth which is periodically liquidated to replenish Bucket 2. The system monitors growth, future liabilities and user-defined rules to provide informative alerts when liquidation actions should be taken to replenish Bucket 2 or for profit taking. Assets in Bucket 3 may also generate income (eg dividends and interest) which may transferred to Bucket 1.

Bucket 4 contains properties such as the user’s main domicile, second homes, and rental properties. Properties can be scheduled for liquidation at a specified time in the future and may also generate income for Bucket 1.

Budgets are categorised groups of items which represent future liabilities. Budgetary items are either regular expenditure (eg groceries) which can be indexed for inflation, or irregular expenditure (eg a future car purchase) which have a due date associated with them. The system provides a default "General" and "Tax" budget and the User can create additional budgets for other categories (eg “Major Purchases” and “Emergency Fund”). Items which represent regular expenditure (eg groceries) can be indexed for inflation. Budgets are tracked against cash spent from Bucket 1. 

The system allows users to model inflows of income from external sources, including regular inflows (e.g., state pension, rental income, annuities) which can be automatically adjusted for inflation and one-off future events (e.g. house sale, inheritance) which have a due date associated with them.  

A rolling cashflow forecast is calculated from the cash in Bucket 1, the budgeted liabilities and projected income from Bucket 2, 4 and external sources. The forecast is used to calculate the required size and timing of new releases from Bucket 2 and also to generate user-defined informative alerts when there is a forecast cash shortfall in Bucket 1 that requires a top-up.

The system maintains up-to-date balances for all user accounts, buckets, and budgets by allowing users to record transactions from the bank and brokerage accounts and by accessing online financial data. Transactions capture every event that affects the portfolio: spending, inflows, asset purchases and sales, dividend and interest payments, transfers, fees, and adjustments. Each transaction stores full details for accounting and analysis: trade and settlement dates, source and destination accounts, currency and exchange rate, budget category, amount, quantity, unit price, fees, taxes, and accrued income. Transactions are given a unique identifier and are recorded using a double-entry structure. The system will initially support manual entry of transactions and future enhancements will allow transactions to be be automatically recorded via bank feeds or APIs. 

Rules consisting of combinations of parametrised logical conditions are used by a Rules engine to generate informative alerts when actions are required. All edits to Rules are versioned with a full change log for auditability.

The system provides a suite of analytical tools for monitoring, analysing, and projecting the performance of the Active and Alternative plans. The analysis will be enhanced by AI in later phases (details TBD).

# Definitions

1.  The User is registered to use the System. Each User has a single Profile
2.  A Profile represents the system identity of the User and holds their Credentials and Preferences and may identify up to two Owners whose assets are managed by that User.
3.  The Credentials consist of a User Name, Password and Membership Tier
4.  A Tier determines the System features to which the User is entitled. It may be either Basic, Premium or Advanced and (details TBD)
5.  The Preferences specify the User's choice of country, language, base currency etc
6.  An Owner has one or more Accounts
7.  An Account holds Units of Assets and is owned by one or both Owners.
8.  An Asset is a transactable instrument classified as either cash, securities or property (other classes may be added in the future).
9.  Cash is either Liquid cash held in a Bank Account that can be spent or Investment Cash held in an Investment Account that can be used to purchase securities.
10. A Security is a tradable financial instrument and includes bonds, equities, and pooled investment vehicles
11. A Property is an Owner's main place of residence, a second home or a rental home. 
12. A Unit is the transactable component of an Asset denominated according to the Asset type(e.g., £1, a share, a gilt or the title to a property).
13. A Bank Account (Current & Deposit) holds Units of Liquid Cash.
14. An Investment Account (Trading, ISA, Pension) holds Units of Securities and Investment Cash
15. An Institution provides Accounts. A Bank provides Current & Deposit Accounts, A Brokerage provides Investment Accounts
16. The Watchlist consists of Securities selected by the User which can be analysed and whose Units can be added to a Brokerage Account using a Transaction.
17. A Plan is a financial model that consists of a Portfolio, Assignments, Budgets, Inflows, Settings and rule Configurations. The Active Plan consists of the Active Portfolio, Active Assignments, Active Settings and Active Configurations. Users can create other Plans that contain different Portfolios, Assignments, Settings and Configurations. 
18. A Portfolio consists of a group of Units. The Active Portfolio contains all the Units in all the Accounts owned by all Owners. Users can create alternative Test Portfolios in which the accounts contain different Units of alternative Assets
19. A Bucket is a user-defined conceptual grouping of Units that serve a specific purpose (Bucket 1 = Liquid Cash, Bucket  2 = Pipeline, Bucket 3 = Growth and Bucket 4 = Property).
20. An Assignment is a mapping of Units in a Portfolio to a Bucket. The Active Assignment maps Units owned by the Owners. Test Assignments can define alternative mappings for the Active Portfolio or mappings of Test Portfolios.
21. A Flow is a planned sequence of scheduled Releases of liquidated assets that are transferred from Bucket 2 ("Pipeline") to Bucket 1 ("Cash") to meet future liabilities.
22. A Release is a user-defined proportion of an Asset in Bucket 2 with a date that specifies when its liquidated proceeds are transferred to Bucket 1.
23. A Rule defines actions to be taken when one or more Conditions is true. Rules are used to generate alerts and and to drive projection logic. 
24. A Condition is a parameterised boolean expression associated with budgets, bucket allocations, performance metrics, risk thresholds, taxation etc
25. A Configuration is a set of Condition parameters used by Rules (eg Pipeline duration, Cash Sufficiency threshold, pension drawdown strategy). The User can create an Active Configuration and Test Configurations. Configurations can be saved and loaded and changes to them are logged.
26. The Rules Engine monitors the Rules and triggers informative alerts related to Active Portfolio (for example, issuing an alert when there is insufficient cash in Bucket 1)
27. A Changelog records changes to the Settings and Configuration associated with the Active Plan
28. A Position is the value of all Units of a specific Asset that are in the User Portfolio.
29. A Holding is the value of all Units of a specific Asset held in a specific Account or assigned to a specific Bucket.
30. A Budget is a user-defined category of expenditure consisting of items that will be purchased with liquid cash (e.g., "Everyday", "Tax payments", "Debt repayments" "Major Items" or "Emergency Fund").
31. A Debit is an expenditure of liquid cash to an external source that is associated with a specific Budget.
32. An Inflow is a user-defined category of income from an external source (eg state pension, rental income, gift)
33. A Credit is an income of liquid cash from an external source that is associated with a specific Inflow. 
34. Budgets and Inflows can be regular or irregular.
35. A Transaction is a user-defined event that causes a change in the number of Units held in an Account, Bucket, or Budget (e.g., a Purchase or Sale of Units, a Credit or Debit, or a Transfer). Transactions are recorded using a double-entry structure with debit and credit entries.
36. An Analysis provides the User was key information about a Portfolio including Growth, Asset allocations, Risk, Asset Correlations, Efficient Frontier etc
37. A Projection is a forecast of Plan's growth, longevity, inheritance estate, tax payments using techniques such as monte-carlo and historical simulations  
38. Settings are external financial parameters (eg RPI, Tax rules, Growth Assumption) used in rules, analyses or projections. The User can create an Active Settings and Test Settings. Settings can be saved and loaded and changes to them are logged. 

## General Capabilities
The Retirement Portfolio Management System will provide the following general capabilities

1. Manage the Active Plan and issue informative alerts based on user-defined rules
2. Analyse the Active Plan and Alternative Plans
3. Create projections for the Active Plan and Alternative Plans

## Requirements Summary

The System will provide the following functionality. Each function will be described in detail by Use-Cases

### Management (Basis for initial MVP)
Users can create an Active Plan for their finances 

1. Allow Users to register on the System and create a Profile for up to two Owners
2. Allow Users to create Accounts at Institutions which can be owned by a single Owner or by both Owners
3. Allow Users to manually input Transactions with future support for automated data feeds using API/Open Banking integration
4. Allow Users to assign Units from their Accounts to Buckets: 1. Cash, 2. Pipeline or 3. Growth. Assets assigned to Bucket 2 may be given a Release date
5. Allow Users to record properties held in Bucket 4 and specify their value and their disposal date (optional)
6. Allow Users to create Budget categories (default categories are "General" and "Tax")
7.  Allow Users to create regular or irregular Budgets consisting of named and priced items
8.  Allow Users to define categories of external credit inflows (regular and ad-hoc)
10. Allow Users to view dashboards showing the value of the Portfolio, Accounts, Assets, Buckets and Budgets

### Analysis (Details TBD)
Users can analyse the Active Plan 

1.  Allow Users to analyse the Portfolio performance, risk, allocations, correlation etc
2.  Allow Users to analyse Bucket performance, income runway etc
3.  Allow Users to analyse Spending, Inflows, Budgets etc
5.  Allow Users to analyse and compare Assets in the Watchlist for growth, Reversion to Mean, CAPE etc
6.  Other types of analysis TBD

### Monitoring (Details TBD)
Users can configure rules that create alerts when the Active Plan meets specified conditions 

1.  Allow Users to configure, save and load Rule Configurations associated with Monitoring
2.  Alert Users when a Bucket 1 cash shortfall is forecast
3.  Alert Users when the Bucket 2 scheduled flow of releases is insufficient to meet the budget liabilities
4.  Alert Users when there are too few scheduled releases in Bucket 2
5.  Alert Users when Bucket 3 liquidation conditions have been met
6.  Alert Users when an Account shortfall is forecast
7.  Alert Users when Portfolio growth is above or below forecast
8.  Alert Users when a Pension withdrawal condition has been met
9.  Allow Users to view a filtered list active Alerts 

### Projections (Details TBD)  
Users can generate projections that forecast the performance of the Plan based on the Portfolio, Assignments, Settings, Configurations, Budgets, Inflows etc. (Details TBD) 

1. Allow Users to configure, save and load Rule Configurations associated with Projections including pension and ISA withdrawal strategies, sale of houses, growth assumptions etc
2. Allow Users to define assumptions for the growth and volatility of Assets in the Portfolio
3. Allow Users to determine how long the Plan will provide sufficient income
4. Allow Users to view a forecast of growth in the Portfolio value 
5. Allow Users to view a forecast of Income
6. Allow Users to view a forecast of Budgets
7. Allow Users to view a forecast of Accounts
8. Allow Users to view a forecast of Tax

### Alternative Plans (Details TBD)

Users can create Alternative Plans that allow alternative Portfolios, Assignments, Settings and Rule Configurations to be analysed, projected and compared. Alternative and Active Plans are equivalent but Alternative Plans are not monitored. (The requirements are deliberately very vague and details TBD).

1. Allow Users to create, save and load Alternative Plans
2. Allow Users to manually configure, save and load Securities for Alternative Plans
3. Allow users to assign Assets to Buckets for Alternative Plans and 
4. Allow Users to configure, save and load Settings and Configurations for an Alternative Plan 
5. Allow Users to normalise Account Balances of Alternative Plans with those in the Active Plan 
6. Allow Users to perform Analysis and Projections of Alternative Plans and compare them

### Operational (Details TBD)
1. Ensure data security and privacy, complying with GDPR and FCA consumer duty requirements (2025).
2. Be accessible on desktop and mobile browsers, meeting WCAG 2.1 standards.
3. Support initial 10 concurrent users, scalable to 100 with AWS migration, handling 50 requests/min (peak 100) initially, scaling to 500 requests/min (peak 1,000).
4. Support an initial load of 50 requests per minute with a 2-second response time (peak 100 requests/min), scalable to 500 requests per minute with AWS (peak 1,000 requests/min), ensuring seamless access.
5. Provide change logging for Settings and Configurations with rollback option (versioned edits, user-initiated revert).

### Interface
The HTML interface contains a vertical menu bar on the left which provides links to pages associated with system features. The landing page is a the Active Plans dashboard. The preliminary list of other pages is shown below (very likely to change). Users can select whether some pages show information about the Active Plan or one of the Alternative Plans. Note that the functionality and layout will be defined later. Settings and Rules are integrated within the relevant pages.

1. Active Plan Dashboard
2. Portfolio (Active and Alternative Plans)
3. Buckets (Active and Alternative Plans)
4. Budgets (Active and Alternative Plans)
5. Income (Active and Alternative Plans)
6. Accounts (Active and Alternative Plans)
7. Transactions (Active Plan Only)
8. Securities (Active and Alternative Plans)
9. Analysis (Active and Alternative Plans and individual/grouped Securities from the Watchlist)
10. Projections (Active and Alternative Plans)
11. Preferences

# Development
The system will be developed with a clear separation of frontend, backend, and database layers. 

## Architectural Principles
- **Modularity**: Modular monolith design
- **Frontend**: The user-facing web application provides an interactive interface. Probably implemented with React, Plotly.js etc
- **Backend**: The application logic processes financial data and manages the database. Probably implemented with Python, gunicorn, uvicorn etc
- **Database**: The persistent storage layer ensures data integrity. Probably implemented with PostgreSQL, with schema hooks for future AI enhancements (e.g., projection tables)

## Development Environment
The tools used to build and test the application locally.
- **Code Editor**: Visual Studio Code with extensions for React, Python, and PostgreSQL.
- **Local Backend**: uvicorn runs the Python backend locally (Flask's development server as a fallback), ensuring API responsiveness.
- **Local Frontend**: npm run dev runs the React development server with hot-reloading, with a proxy (e.g., "http://localhost:8000") routing API calls.
- **Testing**: pytest tests the Python backend; Jest tests the React frontend; built-in browser tools (e.g., Chrome DevTools) supplement UI testing. Linting (flake8, ESLint) and type checking (mypy, TypeScript) ensure code quality.
- **Version Control**: Git with branching (e.g., feature/main) and a .gitignore file excluding sensitive data. The repository is publicly accessible from https://github.com/SRKershaw/Bouquet

## Deployment Sequence

- **Phase 1**: Local Development on PC. This phase focuses on rapid development and iteration.
- **Phase 2**: Ubuntu Server on a Private Network. This phase introduces production practices.
- **Phase 3**: Public Availability via Firewall. This phase secures external 
- **Phase 4**: Migrate to a Cloud Service (AWS). This phase scales with product-market fit.


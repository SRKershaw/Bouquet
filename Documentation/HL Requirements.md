**Retirement Portfolio Management System - High-Level Requirements**
=====================================================================
# Introduction
This document specifies the high-level requirements for a retirement portfolio management system. 

The System provides a suite of integrated tools that support a systematic financial planning strategy that helps UK retirees manage their investment portfolio to generate income and preserve capital. 

The document is intended for an AI chatbot that will assist in the specification, design and implementation of the system. It provides high-level requirements which will be refined into a set of detailed use-cases as part of an iterative and phased development process.  

# Background

Retirees face the challenge of generating a reliable and sufficient income stream from their investment portfolio whilst preserving capital for inheritance. This process is complex, stressful and prone to errors that can lead to cash shortfalls. Although a variety of software tools support discrete tasks such as rebalancing, budgeting and forecasting, none provide a unified solution for individual investors to systematically manage all aspects of  financial planning over their retirement.
  
This system fills that gap by providing an online platform that supports a comprehensive, flexible, rules-based management strategy. It provides functionality to support portfolio structuring, income and capital projection, event alerting, investment analysis and scenario testing. Some functionality will be enhanced with AI in the future.

The system is intended to meet the needs of 55-75-year-old British retirees with £100k+ portfolios who are financially literate and possess moderate IT skills. It will be offered as a tiered subscription service in which the core functionality will be provided free of charge and additional features will be available for recurring fees.

# Overview

The System is based on a hybrid Bucket and Liability-Driven Investment (LDI) strategy that incorporates rules-based guardrails and alerting. This strategy is intended to mitigate market volatility and aid financial planning by structuring a portfolio into time-segmented groupings ("Buckets") whose size and composition are determined by budgeted cash-flow requirements. The LDI functionality aligns Bucket 2’s income pipeline with budgeted liabilities, ensuring releases match future spending needs adjusted for inflation. The system analyses and monitors users' portfolios to provide information that supports the implementation of the strategy. 

The system is based around four buckets:
- **Bucket 1** holds cash for spending.
- **Bucket 2** holds medium-term, low-volatility assets to provide a pipeline of income.
- **Bucket 3** holds long-term assets to generate growth.
- **Bucket 4** holds properties that generate growth and may provide rental income.

The growth generated from assets in Bucket 3 is used to purchase assets for Bucket 2, which provide a scheduled flow of income for Bucket 1. Bucket 4 may be used to provide additional income for Bucket 1.

Cash in Bucket 1 is monitored against rolling budgets and guardrails to create alerts that notify users if a shortfall is forecast, enabling them to take actions such as reducing spending or topping up. Alerts are triggered by user-defined rules based on metrics such as cashflow risk, pipeline length, or asset performance thresholds.

Assets in Bucket 2 provide an income pipeline consisting of a flow of scheduled release events whose frequency, size, and duration are user-configurable. Each release is intended to transfer sufficient cash into Bucket 1 to support the forecast budget demands until the next scheduled release. The number of releases determines how many years of spending are supported by the pipeline. Assets not in the flow (i.e. those with no release date) represent a buffer that can be liquidated and transferred to Bucket 1 on an ad-hoc basis (e.g., for interim shortfalls). Each release may contain a combination of:
- Bonds, whose maturity date determines the release date.
- Other securities with user-defined release dates.
- Scheduled dividends or interest payments.

Assets in Bucket 3 generate growth which is periodically liquidated to replenish Bucket 2. The system monitors growth, future liabilities and user-defined rules to provide informative alerts when liquidation actions should be taken.

Assets in Bucket 3 generate growth which is periodically liquidated to replenish Bucket 2. The system monitors growth, future liabilities, and user-defined rules ro provide informative alerts recommending when specified liquidation actions should be taken to maintain the Bucket 2 Pipeline or for profit taking.

Bucket 4 contains properties such as the user’s main domicile, second homes, and rental properties. Properties can be scheduled for liquidation at a specified time in the future and may also generate income for Bucket 1.

The system allows users to create budgets for different spending categories, each representing a future liability. Budgets are tracked against actual spending. Default “Everyday” and "Tax" budgets may be specified by the user or automatically estimated by the system. Users can add budgets for categories such as “Major Purchases” or “Emergency Fund.” Items within budgets can be indexed for inflation and changing lifestyle to calculate the size of future pipeline releases and project long-term liabilities.

The system allows users to model income from external sources, including regular income (e.g., state pension, rental income, annuities) which can be automatically adjusted for inflation and one-off future events (e.g. house sale, inheritance).

A rolling cashflow forecast is calculated from the cash in Bucket 1, scheduled liabilities from budgets, and projected income from Bucket 2, 4 and external sources. The forecast is used to calculate the required size and timing of new releases from Bucket 2 and also to generate user-defined informative alerts when there is a forecast cash shortfall in Bucket 1 that requires a top-up.

The system maintains up-to-date balances for all user accounts, buckets, and budgets by recording transactions from the user's bank and brokerage accounts and by accessing online financial data. Transactions capture every event that affects the portfolio: spending, external income, asset purchases and sales, dividend and interest payments, transfers, fees, and adjustments. Each transaction stores full details for accounting and analysis: trade and settlement dates, source and destination accounts, currency and exchange rate, linked security or budget category, amount, quantity, unit price, fees, taxes, and accrued income. Every entry carries a unique identifier and is recorded using a double-entry structure. The system relies of manual transaction entry initially, with an architecture designed to accommodate future automation via bank feeds or APIs. All edits are versioned with a full change log for auditability.

The system provides a suite of analytical tools, some which will be enhanced by AI (to be specified in later phases), for monitoring, analysing, and forecasting portfolio performance and tax planning. These tools draw on transaction history and market data to calculate returns, risk metrics, cashflow projections, and asset allocations. Users can run scenario tests, perform risk and stress testing, and model alternative investment or withdrawal strategies, enabling evidence-based decisions and forward-looking planning.

# Terminology

1. A User is a person that is registered to use the System
2. A Profile contains a User's financial data and configuration settings.
3. Each Profile may contain two Owners (typically partners), one of whom is the User
4. An Owner owns Assets that are in the Portfolio
5. An Asset is a transactable instrument classified as either cash, securities (including bonds, equities, and pooled investment vehicles), or property.
6. A Unit is the transactable component of an asset (e.g., £1, a share, or a bond).
7. A Portfolio consists of all Units owned by all Owners.
8. An Account holds Units and is owned by one or both Owners
9. A Bank Account (Current & Deposit) holds Cash.
10. A Brokerage Account (Trading, ISA, SIPP) holds Cash and Securities.
11. An Institution (e.g., a bank or brokerage) provides Accounts.
12. A Property is an Owner's main place of residence, a second home or a rental home
13. A Bucket is a user-defined grouping of Units for a specific purpose (Bucket 1 = Cash, Bucket  2 = Pipeline, Bucket 3 = Growth and Bucket 4 = Property).
14. A Position is the value of all Units of a specific Asset that are in the Portfolio.
15. A Holding is the value of all Units of a specific Asset held in a specific Account or assigned to a specific Bucket.
16. A Flow is a sequence of scheduled Releases of liquidated assets that are transferred from Bucket 2 ("Pipeline") to Bucket 1 ("Cash") to meet future liabilities.
17. A Release is a user-defined proportion of an Asset in Bucket 2 with a date that specifies when its liquidated proceeds are transferred to Bucket 1.
18. A Budget is a user-defined grouping of future liabilities that defines a target amount of spending on a specified category (e.g., "Everyday", "Tax", "Debt" "Major", "Emergency") over a specified time period.
19. A Liability is a spending obligation at a defined date
20. A Transaction is a user-defined event that causes a change in the number of Units held in an Account, Bucket, or Budget (e.g., a Purchase or Sale of Units, a Credit or Debit, or a Transfer), recorded using a double-entry structure with debit and credit entries.
21. A Rules Engine allows conditions and guardrails to be defined and logically combined to trigger informative alerts when management actions are required.
22. Conditions are time-based or threshold-based parameterised expressions associated with budgets, bucket allocations, performance, risk, and tax.
23. A Guardrail is a parameterised constraint that acts as a protective safety mechanism within the rules engine framework.
24. A Projection is an analysis of the Portfolio that forecasts portfolio longevity, inheritance estate, tax payments etc using techniques such as monte-carlo and historical simulations  
25. A Scenario is a user-defined selection of Assets, Bucket assignments, Conditions and Guardrails that can be analysed and projected to compare alternative strategies.

# Requirements Summary
The Retirement Portfolio Management System will provide the following core functions. Each function will be described in detail by a Use-Case

## Management
1. Allow Users to register on the System and create a Profile with up to two Owners
2. Allow Users to create Accounts at Institutions which can be owned by a single Owner or by both Owners
3. Allow Users to manually input Transactions with future support for automated data feeds using API/Open Banking integration
4. Allow Users to assign Units from their Accounts to Buckets: 1. Cash, 2. Pipeline or 3. Growth. Assets assigned to Bucket 2 may be given a Release date
5. Allow Users to record properties held in Bucket 4 and specify their value and, optionally, their rental income and disposal date
6. Allow Users to create Budget categories (default categories are "General" and "Tax")
7.  Allow Users to create recurrent or dated items in Budgets
8.  Allow Users to define external sources of Income (regular and ad-hoc)
9.  Allow users to define portfolio Settings such as RPI, base rates, growth rates, SIPP strategy etc.
10. Allow Users to view dashboards showing the value of the Portfolio, Accounts, Assets, Buckets and Budgets

## Forecasting
Forecasts are calculated from balances, growth, budgets, flows and incomes. Users can define rules for the forecast (eg ISA/SIPP withdrawal rules for tax modelling)
1. Allow Users to view forecasts of the Portfolio
2. Allow Users to view forecasts of Bucket 1 that identify potential shortfalls
3. Allow Users to view forecasts of Bucket 2 releases
4. Allow Users to view forecasts of Bucket 3 and Bucket 4 growth
5. Allow Users to view forecasts of Income
6. Allow Users to view forecasts of Budgets
7. Allow Users to view forecasts of Accounts
8. Allow Users to view forecasts of Tax

## Monitoring
Users can define parameters for Conditions (TBD) and Thresholds (TBD), related to the Portfolio, Accounts and Buckets, that are used by the rules engine to generate informative alerts

1.  Alert Users when a Bucket 1 cash shortfall is forecast
2.  Alert Users when the Bucket 2 scheduled flow of releases is insufficient to meet the budget liabilities
3.  Alert Users when there are too few scheduled releases in Bucket 2
4.  Alert Users when Bucket 3 liquidation conditions have been met
5.  Alert Users when an Account shortfall is forecast
6.  Alert Users when a Portfolio shortfall is forecast
7.  Alert Users when a SIPP withdrawal condition has been met
8.  Alert Users when a Tax condition has been met
9.  Allow Users to view all Alerts 
10. Allow Users to view the status of Accounts, Buckets etc conditions 

## Analysis
Users can examine the performance (eg growth, ROI) of their Portfolio from a variety of perspectives (TBD)

1.  Allow Users to view an analysis of the performance of the Portfolio
2.  Allow Users to view an analysis of the Buckets
3.  Allow Users to view an analysis of the Assets 
4.  Allow Users to view an analysis of the SIPPs
5.  Allow Users to view an analysis of tax

## Scenarios
1. Allow Users to define, forecast and analyse Scenarios that represent alternative investment portfolios, bucket assignments, rules and settings. Details TBD

## Operational
1. Ensure data security and privacy, complying with GDPR and FCA consumer duty requirements (2025).
2. Be accessible on desktop and mobile browsers, meeting WCAG 2.1 standards.
3. Support initial 10 concurrent users, scalable to 100 with AWS migration." with "Support initial 10 concurrent users, scalable to 100 with AWS migration, handling 50 requests/min (peak 100) initially, scaling to 500 requests/min (peak 1,000).
4. Support an initial load of 50 requests per minute with a 2-second response time, scalable to 500 requests per minute with AWS, ensuring seamless access." with "Support an initial load of 50 requests per minute with a 2-second response time (peak 100 requests/min), scalable to 500 requests per minute with AWS (peak 1,000 requests/min), ensuring seamless access..

## Interface
The HTML interface contains a vertical menu bar on the left which provides links to pages associated with system features. The landing page is a dashboard and the other main pages are shown below. Note that the functionality and layout will be defined later.

1. Portfolio
2. Buckets
3. Budgets
4. Income
5. Accounts
6. Transactions
7. Securities
8. Scenarios


## Phasing

To ensure the System will deliver value incrementally while maintaining an extensible architecture. Development will follow a phased functional release plan as shown below. This will be refined as detail is added to the requirements and use-cases. 

- **Phase 1** Management Functionality
- **Phase 2** Forecasting Functionality
- **Phase 3** Monitoring
- **Phase 4** Analysis
- **Phase 5** Scenarios
- **Phase 6** AI Enhancements 
  
The deployment release plan is shown below
- **Deployment 1** Ubuntu server on home network
- **Deployment 2** Ubuntu server with public access
- **Deployment 3** Public Cloud Service (eg AWS)


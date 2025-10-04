
**Retirement Portfolio Management System - Use Cases**
=====================================================================

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
**Postconditions**: Asset is available for transactions and bucket configurations.    
**Basic Flow**:  
- User selects the option to add an asset.  
- User enters asset details (e.g., type, name, ticker for securities, valuation for property, maturity date for bonds).  
- System validates inputs (e.g., unique ticker for securities using external market data, positive valuation for property, valid maturity date for bonds).  
- System adds the asset to the catalogue.  


## Create a Transaction
**Description**: Record a transaction (e.g., purchase, sale, income) with double-entry, associating dividends/coupons with releases if applicable.  
**Actors**: User, System.  
**Preconditions**: Accounts and assets exist; user is authenticated. 
**Postconditions**: Portfolio is updated with audit trail; allocations refreshed.    
**Basic Flow**:  
- User selects the option to create a transaction.  
- User specifies transaction type (e.g., purchase, sale, transfer).  
- User enters details (e.g., date, amount, asset, account, bucket, fees, taxes, ReleaseId for dividends/coupons).  
- System validates inputs (e.g., sufficient funds, no negative balances, ownership consistency, FX rates).  
- System records the transaction using a double-entry structure, updates balances/logs, and auto-associates dividends/coupons to Release via asset match if ReleaseId provided.  
- System logs the transaction with a unique identifier and versioned history.  


## Manage Bucket Assignments
**Description**: Assign, unassign, or transfer units between buckets.  
**Actors**: User, System.  
**Preconditions**: Buckets and assets exist; user is authenticated.   
**Postconditions**: Buckets reflect new assignments.     
**Basic Flow**:  
- User selects action (assign, unassign, or transfer) and specifies source/destination buckets and units.  
- System validates inputs (e.g., sufficient units).  
- System updates holdings as a transaction with double-entry structure.  
- System logs the transaction.  


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

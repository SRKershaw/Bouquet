**Retirement Portfolio Management System - Class Definitions**
=====================================================================

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

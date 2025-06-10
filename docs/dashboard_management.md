# Dashboard Management

The dashboard management module provides an overview of key metrics and statistics related to the discount management system, including points activity and tier distribution.

## Key Components

### Routes

The following route is defined for the dashboard:

- `GET /loyalty/dashboard`: Show the dashboard.

### Controllers

The `DashboardController` is responsible for generating and displaying the dashboard. Here are some key methods:

- **Dashboard**
  - `dashboard(TierServiceInterface $tierService)`: Generate and display the dashboard with points activity and tier distribution data.

### Entities

The `DashboardController` interacts with several entities to gather data:

- `PointTransaction`: Represents point transactions in the system.
- `Tier`: Represents different tiers in the system.
- `Member`: Represents members in the system.

### Services

The `TierServiceInterface` is used to fetch tier-related data:

- **Get Member Tier**
  - `getMemberTier(Member $member): ?Tier`: Get the tier for a specific member.
  
- **Get All Tiers**
  - `getAllTiers(): Collection<int, Tier>`: Get all tiers in ascending order of required points.

### Data Generation

The `DashboardController` generates data for the dashboard in the following ways:

#### Points Activity Data

- **Earned Points**
  - Filters `PointTransaction` records where `direction` is `in` and `transaction_type` is `earn`.
  - Groups by month and calculates the total points earned.
  
- **Redeemed Points**
  - Filters `PointTransaction` records where `direction` is `out` and `transaction_type` is `redeem`.
  - Groups by month and calculates the total points redeemed.
  
- **Expired Points**
  - Filters `PointTransaction` records where `direction` is `out` and `transaction_type` is `expire`.
  - Groups by month and calculates the total points expired.

#### Tier Distribution Data

- **Tier Counts**
  - Retrieves all tiers.
  - Counts the number of members in each tier using the `TierServiceInterface`.


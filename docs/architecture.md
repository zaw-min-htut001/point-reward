# Architecture

The Member Points Loyalty Program is a modular Laravel application (`Modules\DiscountManagement`) for managing loyalty points, tiers, rewards, and campaigns.

## Components
- **Entities**:
  - `Member`: Polymorphic (`memberable_id`, `memberable_type`) linked to `Contact` or `BusinessUser`.
  - `Wallet`: Stores points (`balance`) with `point_type_id`, `is_primary`.
  - `PointType`: Defines points (`name`, `is_expirable`, `default_expiry_days`, `exchange_rate`).
  - `Reward`: Redeemable items (`point_cost`, `type`, `stock`, `min_tier_id`).
  - `Tier`: Tiers (`min_points`, `multiplier`, `perks`).
  - `MemberPointsSummary`: Tracks `total_earned`, `total_redeemed`, `total_expired`, `balance`.
  - `PointTransaction`: Logs changes (`amount`, `direction`, `transaction_type`, `meta`).
  - `Campaign`: Defines reward promotions (`name`, `start_date`, `end_date`).
- **Services**:
  - `TierService`: Calculates tiers (`getMemberTier`, `getNextTier`).
  - `RewardService`: Manages redemption.
- **Controllers**:
  - `PointTypeController`: Manages point types.
  - `MemberController`: Handles members and dashboard.
  - `WalletController`: Manages earning/redeeming points.
  - `TierController`: Tier CRUD.
  - `RewardController`: Reward CRUD.
  - `CampaignController`: Campaign management.
  - `TransactionController`: Transaction views.
  - `DashboardController`: System overview.
- **Views**:
  - `point-types/*`: Point type management.
  - `members/*`: Member operations and dashboard.
  - `wallets/*`: Wallet actions.
- **Enums**:
  - `TransactionType`: `EARN`, `REDEEM`, `REFUND`, `ADJUST`, `MINT`.

## Database Schema
| Table                  | Key Columns                              | Description                                      |
|------------------------|------------------------------------------|--------------------------------------------------|
| `members`             | `id`, `memberable_id`, `memberable_type`, `member_code`, `is_active` | Polymorphic members. |
| `wallets`             | `id`, `owner_id`, `owner_type`, `balance`, `point_type_id`, `is_primary`, `status` | Points storage. |
| `point_types`         | `id`, `name`, `is_expirable`, `default_expiry_days`, `exchange_rate` | Point definitions. |
| `rewards`             | `id`, `name`, `description`, `point_cost`, `type`, `stock`, `min_tier_id` | Redeemable items. |
| `tiers`               | `id`, `name`, `min_points`, `multiplier`, `perks` | Tier definitions. |
| `member_points_summaries` | `id`, `member_id`, `point_type_id`, `total_earned`, `total_redeemed`, `total_expired`, `balance` | Points tracking. |
| `point_transactions`  | `id`, `wallet_id`, `point_type_id`, `amount`, `direction`, `transaction_type`, `source`, `description`, `meta` | Transaction logs. |
| `campaigns`           | `id`, `name`, `start_date`, `end_date`   | Reward campaigns. |

## ERD
```mermaid
erDiagram
    MEMBER ||--o{ WALLET : owns
    MEMBER ||--o{ MEMBER_POINTS_SUMMARY : has
    WALLET ||--o{ POINT_TRANSACTION : has
    REWARD ||--o{ TIER : requires
    POINT_TYPE ||--o{ WALLET : defines
    CAMPAIGN ||--o{ REWARD : promotes
```

    MEMBER {
        int id
        int memberable_id
        string memberable_type
        string member_code
        boolean is_active
        timestamp created_at
        timestamp updated_at
    }
    WALLET {
        int id
        int owner_id
        string owner_type
        float balance
        int point_type_id
        boolean is_primary
        string status
        timestamp created_at
        timestamp updated_at
    }
    POINT_TYPE {
        int id
        string name
        boolean is_expirable
        int default_expiry_days
        float exchange_rate
        timestamp created_at
        timestamp updated_at
    }
    REWARD {
        int id
        string name
        string description
        float point_cost
        string type
        int stock nullable
        int min_tier_id nullable
        timestamp created_at
        timestamp updated_at
    }
    TIER {
        int id
        string name
        float min_points
        float multiplier
        string perks nullable
        timestamp created_at
        timestamp updated_at
    }
    MEMBER_POINTS_SUMMARY {
        int id
        int member_id
        int point_type_id
        float total_earned
        float total_redeemed
        float total_expired
        float balance
        timestamp created_at
        timestamp updated_at
    }
    POINT_TRANSACTION {
        int id
        int wallet_id
        int point_type_id
        float amount
        string direction
        string transaction_type
        string source
        string description nullable
        json meta nullable
        timestamp created_at
        timestamp updated_at
    }
    CAMPAIGN {
        int id
        string name
        date start_date
        date end_date
        timestamp created_at
        timestamp updated_at
    }

## WORKFLOW
- **Point earning**:
```mermaid
graph TD
    A[Start: POST /loyalty/wallets/earn/pos] --> B{Validate Request}
    B -->|Valid points, sale_id| C[Fetch Member and Wallet]
    B -->|Invalid| D[Return Error]
    C --> E[Call TierService::applyTierMultiplier]
    E --> F{Query Tier based on total_earned}
    F --> G[Apply Multiplier]
    G --> H[Update Wallet Balance]
    H --> I[Update MemberPointsSummary]
    I --> J[Log PointTransaction]
    J --> K[Return Success]
    K --> L[End]
```
- **Reward Redemption**:
```mermaid
graph TD
    A[Start: POST /loyalty/wallets/wallet_id/redeem] --> B{Validate Request}
    B -->|Valid reward_id| C[Fetch Wallet and Reward]
    B -->|Invalid| D[Return Error]
    C --> E{Sufficient Points?}
    E -->|Yes| F{Tier Eligible?}
    E -->|No| G[Insufficient Points Error]
    F -->|Yes| H{Stock Available?}
    F -->|No| I[Ineligible Tier Error]
    H -->|Yes| J[Deduct Points]
    H -->|No| K[Out of Stock Error]
    J --> L[Update Reward Stock]
    L --> M[Log PointTransaction]
    M --> N[Apply Free Shipping if Gold]
    N --> O[Return Success]
    O --> P[End]
```
- **Dashboard**:
```mermaid
graph TD
    A[Start: GET /loyalty/members/member_id/dashboard] --> B{Authenticate User}
    B -->|Authorized| C[Fetch Member]
    B -->|Unauthorized| D[Return 403]
    C --> E[Fetch Primary Wallet]
    E --> F{Points Summary Exists?}
    F -->|Yes| G[Load Summary]
    F -->|No| H[Create Summary]
    G --> I[Get Current Tier]
    H --> I
    I --> J[Get Next Tier]
    J --> K[Calculate Progress]
    K --> L[Fetch Last 10 Transactions]
    L --> M[Fetch Eligible Rewards]
    M --> N[Prepare Chart Data]
    N --> O[Render Dashboard View]
    O --> P[End]
```    
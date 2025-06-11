# Architecture

The Member Points Loyalty Program is a modular Laravel application (`Modules\DiscountManagement`) for managing loyalty points, tiers, rewards, and campaigns.

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
graph TD
    A[Start] --> B[Create Point Type]
    B --> C[Create Primary Wallet]
    C --> D[Admin Creates Member]
    D --> E[Create Member Wallet]
    E --> F[Select Action]
    F -->|Earn| G[Call earnFromPurchase]
    F -->|Redeem| H[Call redeemPoints/redeemReward]
    F -->|Adjust| I[Call adjustPoints]
    F -->|Mint| J[Call mintPoints]
    F -->|Refund| K[Call refundPoints]
    G --> L[Update Wallet Balance]
    H --> L
    I --> L
    J --> L
    K --> L
    L --> M[Log PointTransaction]
    M --> N[Log WalletTransaction]
    N --> O[End]
```

```mermaid
classDiagram
class Member {
    int id
    int memberable_id
    string memberable_type
    string member_code
    boolean is_active
    timestamps
}

class Wallet {
    int id
    int owner_id
    string owner_type
    float balance
    int point_type_id
    bool is_primary
    string status
    timestamps
}

class PointType {
    int id
    string name
    bool is_expirable
    int default_expiry_days
    float exchange_rate
    timestamps
}

class PointTransaction {
    int id
    int wallet_id
    int point_type_id
    float amount
    string direction
    string transaction_type
    string source
    string description
    json meta
    datetime expires_at
    timestamps
}

class MemberPointsSummary {
    int id
    int member_id
    int point_type_id
    float total_earned
    float total_redeemed
    float total_expired
    float balance
    timestamps
}

class Reward {
    int id
    string name
    string description
    float point_cost
    string type
    int stock
    int min_tier_id
    timestamps
}

class Tier {
    int id
    string name
    float min_points
    float multiplier
    string perks
    timestamps
}

class PromoCampaign {
    int id
    string name
    int point_type_id
    float total_budget
    float issued_so_far
    date start_date
    date end_date
    boolean active
    timestamps
}

class WalletTransaction {
    int id
    int from_wallet_id
    int to_wallet_id
    float amount
    string transaction_type
    string reference_type
    int reference_id
    string status
    timestamps
}
```

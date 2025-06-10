# Transaction Management

The transaction management module allows you to view and manage point transactions and wallet transactions.

## Key Components

### Routes

The following routes are defined for managing transactions:

- `GET /loyalty/transactions/point`: List all point transactions.
- `GET /loyalty/transactions/wallet`: List all wallet transactions.

### Controllers

The `TransactionController` is responsible for handling transaction-related operations. Here are some key methods:

- **Point Transactions**
  - `pointTransactions()`: List all point transactions. Includes pagination and formatted transaction details.
  
- **Wallet Transactions**
  - `walletTransactions()`: List all wallet transactions. Includes pagination and formatted transaction details.

### Entities

The `PointTransaction` entity represents a point transaction in the system. Here are the key attributes:

- `id`: Unique identifier for the point transaction.
- `wallet_id`: ID of the wallet involved in the transaction.
- `point_type_id`: ID of the point type.
- `amount`: Amount of points involved in the transaction.
- `direction`: Direction of the transaction (e.g., earned, redeemed).
- `transaction_type`: Type of the transaction (e.g., purchase, mint).
- `source`: Source of the transaction.
- `expires_at`: Expiration date of the transaction.
- `status`: Status of the transaction.
- `description`: Description of the transaction.
- `created_at`: Timestamp of when the transaction was created.

The `WalletTransaction` entity represents a wallet transaction in the system. Here are the key attributes:

- `id`: Unique identifier for the wallet transaction.
- `from_wallet_id`: ID of the source wallet.
- `to_wallet_id`: ID of the destination wallet.
- `amount`: Amount of points transferred.
- `transaction_type`: Type of the transaction (e.g., transfer, refund).
- `reference_type`: Type of the reference entity (e.g., sale, point transaction).
- `reference_id`: ID of the reference entity.
- `status`: Status of the transaction.
- `created_at`: Timestamp of when the transaction was created.

### Formatting and Normalization

The `TransactionController` includes methods to format and normalize transaction data for better readability and consistency:

- **Point Transactions**
  - Normalize the `source` field to human-readable formats.
  - Determine the owner name based on the wallet owner type (e.g., member, business wallet).
  
- **Wallet Transactions**
  - Determine the `from_wallet` and `to_wallet` names based on the wallet owner type (e.g., member, business wallet).
  - Normalize the `reference` field to human-readable formats.

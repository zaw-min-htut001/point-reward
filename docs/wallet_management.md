# Wallet Management

Manages wallet operations, including creation, point earning, redemption, refunds, adjustments, minting, and ledger display.

## Key Components

### Routes

The following routes are defined for managing wallet operations:

  - `GET /loyalty/wallets`: List all wallets.
  - `POST /loyalty/wallets`: Create a new wallet.
  - `GET /loyalty/wallets/{wallet}/balance`: Get balance for a specific wallet.
  - `GET /loyalty/wallets/{member}/earn`: Show earn modal for a specific member.
  - `POST /loyalty/wallets/earn/erp`: Earn points from ERP.
  - `POST /loyalty/wallets/earn/pos`: Earn points from POS.
  - `POST /loyalty/wallets/earn/ecommerce`: Earn points from eCommerce.
  - `GET /loyalty/wallets/{wallet}/redeem`: Show redeem modal for a specific wallet.
  - `POST /loyalty/wallets/{wallet}/redeem`: Redeem points for a specific wallet.
  - `GET /loyalty/wallets/{wallet}/refund`: Show refund modal for a specific wallet.
  - `POST /loyalty/wallets/refund`: Refund points.
  - `GET /loyalty/wallets/{wallet}/adjust`: Show adjust modal for a specific wallet.
  - `POST /loyalty/wallets/{wallet}/adjust`: Adjust points for a specific wallet.
  - `GET /loyalty/wallets/{wallet}/mint-modal`: Show mint modal for a specific wallet.
  - `POST /loyalty/wallets/{wallet}/mint`: Mint points for a specific wallet.
  - `GET /loyalty/wallets/{wallet}/ledger`: Show ledger for a specific wallet.
  - `GET /loyalty/wallets/available/{member}`: List available wallets for a specific member.

### Controllers

The `WalletController` manages wallet operations:

- **Index**
  - `index()`: List all wallets.
- **Create**
  - `create(Request $request, WalletServiceInterface $walletService)`: Create a new wallet.
- **Get Balance**
  - `getBalance(Wallet $wallet)`: Get wallet balance.
- **Earn Modal**
  - `earnModal(Member $member)`: Show modal to earn points.
- **Earn Points**
  - `earnFromErp`, `earnFromPos`, `earnFromEcommerce`: Earn points from different channels.
- **Redeem Modal**
  - `redeemModal(Wallet $wallet)`: Show modal to redeem rewards.
- **Redeem Reward**
  - `redeem(Request $request, Wallet $wallet, RewardServiceInterface $rewardService)`: Redeem a reward.
- **Refund Modal**
  - `refundModal(Wallet $wallet)`: Show modal to refund points.
- **Refund Points**
  - `refund(Request $request, PointRefundServiceInterface $refundService)`: Refund points.
- **Adjust Modal**
  - `adjustModal(Wallet $wallet)`: Show modal to adjust points.
- **Adjust Points**
  - `adjust(Request $request, Wallet $wallet, PointAdjustmentServiceInterface $adjustService)`: Adjust points.
- **Mint Modal**
  - `mintModal(Wallet $wallet)`: Show modal to mint points.
- **Mint Points**
  - `mint(Request $request, Wallet $wallet, PointAdjustmentServiceInterface $adjustService)`: Mint points.
- **Ledger**
  - `ledger(Wallet $wallet)`: View wallet transaction ledger.
- **Available Wallets**
  - `availableWallets(Request $request, Member $member)`: Get available member wallets.

### Entities

The `Wallet` entity stores points for members or the business:

- `id`: Unique identifier.
- `owner_id`: ID of the owner (Member or PointType).
- `owner_type`: Type of owner (Member or PointType).
- `point_type_id`: ID of the point type.
- `balance`: Current points balance.
- `is_primary`: Boolean indicating primary wallet.
- `status`: Wallet status (e.g., active).

### Services

The `WalletServiceInterface`, `PointEarningServiceInterface`, `RewardServiceInterface`, `PointRefundServiceInterface`, and `PointAdjustmentServiceInterface` handle wallet and point operations:

- **WalletService**: Creates or retrieves wallets.
- **PointEarningService**: Manages point earning from purchases or other sources.
- **RewardService**: Handles reward redemption.
- **PointRefundService**: Processes point refunds.
- **PointAdjustmentService**: Adjusts or mints points.

## WORKFLOW
- **Create Wallet**: 
```mermaid
graph TD
    A[Start: POST /loyalty/wallets] --> B{Authenticate User}
    B -->|Authorized| C{Validate Request}
    B -->|Unauthorized| D[Return 403]
    C -->|Valid owner_id, owner_type, point_type_id| E[Call WalletService::getWallet]
    C -->|Invalid| F[Return Validation Error]
    E --> G[Redirect with Success]
    G --> H[End]
```    
- **Earn Points from POS**:
```mermaid
graph TD
    A[Start: POST /loyalty/wallets/earn/pos] --> B{Authenticate User}
    B -->|Authorized| C{Validate Request}
    B -->|Unauthorized| D[Return 403]
    C -->|Valid wallet_id, amount| E[Fetch Wallet]
    C -->|Invalid| F[Return Validation Error]
    E --> G{Check Wallet Exists?}
    G -->|Yes| H[Call earnFromPurchase]
    G -->|No| I[Return 404]
    H --> J{AJAX Request?}
    J -->|Yes| K[Return JSON Success]
    J -->|No| L[End]
    K --> M[End]
```    
- **Redeem Reward**:
```mermaid
graph TD
    A[Start: POST /loyalty/wallets/redeem] --> B{Authenticate User}
    B -->|Authorized| C{Validate Request}
    B -->|Unauthorized| D[Return 403]
    C -->|Valid reward_id| E[Fetch Reward]
    C -->|Invalid| F[Return Validation Error]
    E --> G{Fetch Member Tier}
    G --> H{Check Tier Eligibility}
    H -->|Eligible| I[Check Gold Tier]
    H -->|Not Eligible| J[Return 422 Error]
    I --> K{Apply Free Shipping?}
    K -->|Yes| L[Set Free Shipping]
    K -->|No| M[Call redeemReward]
    L --> M
    M --> N{AJAX Request?}
    N -->|Yes| O[Return JSON Success]
    N -->|No| P[Redirect with Success]
    O --> Q[End]
    P --> Q
    J --> Q
```
- **Refund Points**:
```mermaid
graph TD
    A[Start: POST /loyalty/wallets/refund] --> B{Authenticate User}
    B -->|Authorized| C{Validate Request}
    B -->|Unauthorized| D[Return 403]
    C -->|Valid wallets, amount, reference| E[Fetch Wallets]
    C -->|Invalid| F[Return Validation Error]
    E --> G{Check Business-to-Member?}
    G -->|Yes| H[Call refundPoints]
    G -->|No| I[Return 422 Error]
    H --> J{AJAX Request?}
    J -->|Yes| K[Return JSON Success]
    J -->|No| L[Redirect with Success]
    K --> M[End]
    L --> M
    I --> M
```    
- **Adjust Points**:
```mermaid
graph TD
    A[Start: POST /loyalty/wallets/adjust] --> B{Authenticate User}
    B -->|Authorized| C{Validate Request}
    B -->|Unauthorized| D[Return 403]
    C -->|Valid amount, reason| E[Call adjustPoints]
    C -->|Invalid| F[Return Validation Error]
    E --> G{AJAX Request?}
    G -->|Yes| H[Return JSON Success]
    G -->|No| I[Redirect with Success]
    H --> J[End]
    I --> J
```
- **Mint Points**:
```mermaid
graph TD
    A[Start: POST /loyalty/wallets/mint] --> B{Authenticate User}
    B -->|Authorized| C{Check Business Wallet?}
    B -->|Unauthorized| D[Return 403]
    C -->|Yes| E{Validate Request}
    C -->|No| F[Return Error]
    E -->|Valid amount, reason| G[Call mintPoints]
    E -->|Invalid| H[Return Validation Error]
    G --> I{AJAX Request?}
    I -->|Yes| J[Return JSON Success]
    I -->|No| K[Redirect with Success]
    J --> L[End]
    K --> L
    F --> L
```    
- **View Ledger**:
```mermaid
graph TD
    A[Start: GET /loyalty/wallets/ledger] --> B{Authenticate User}
    B -->|Authorized| C[Fetch PointTransactions]
    B -->|Unauthorized| D[Return 403]
    C --> E[Load Wallet Owner]
    E --> F[Format Owner Name]
    F --> G[Normalize Source]
    G --> H[Format Transactions]
    H --> I[Render Ledger View]
    I --> J[End]
```    
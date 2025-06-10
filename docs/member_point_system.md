# Member Point System

The member point system is a core component of the Discount Management module. It handles the accumulation, redemption, and management of points for members.

## Key Components

### Routes

The following routes are defined for managing member points:

- **Point Types**
  - `GET /loyalty/point-types`: List all point types.
  - `GET /loyalty/point-types/{pointType}/confirm-delete`: Confirm deletion of a point type.
  
- **Members**
  - `GET /loyalty/members`: List all members.
  - `GET /loyalty/members/create`: Show form to create a new member.
  - `POST /loyalty/members`: Store a new member.
  - `GET /loyalty/members/{member}/wallets`: List wallets for a specific member.
  - `GET /loyalty/members/{member}/dashboard`: Show dashboard for a specific member.
  
- **Transactions**
  - `GET /loyalty/transactions/point`: List point transactions.
  - `GET /loyalty/transactions/wallet`: List wallet transactions.
  
- **Wallets**
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
  
- **Campaigns**
  - `GET /loyalty/campaigns`: List all campaigns.
  - `GET /loyalty/campaigns/{campaign}/confirm-delete`: Confirm deletion of a campaign.
  - `GET /loyalty/campaigns/{campaign}/report`: Generate report for a specific campaign.
  
- **Tiers**
  - `GET /loyalty/tiers`: List all tiers.
  - `GET /loyalty/tiers/{tier}/confirm-delete`: Confirm deletion of a tier.
  
- **Rewards**
  - `GET /loyalty/rewards`: List all rewards.
  - `GET /loyalty/rewards/{reward}/confirm-delete`: Confirm deletion of a reward.
  - `GET /loyalty/rewards/catalog`: Show rewards catalog.
  
- **Dashboard**
  - `GET /loyalty/dashboard`: Show dashboard.

### Controllers

The `WalletController` is responsible for handling wallet-related operations. Here are some key methods:

- **Index**
  - `index()`: List all wallets.
  
- **Create**
  - `create(Request $request, WalletServiceInterface $walletService)`: Create a new wallet.
  
- **Get Balance**
  - `getBalance(Wallet $wallet)`: Get balance for a specific wallet.
  
- **Earn Points**
  - `earnModal(Member $member)`: Show earn modal for a specific member.
  - `earnFromErp(Request $request, PointEarningServiceInterface $earningService)`: Earn points from ERP.
  - `earnFromPos(Request $request, PointEarningServiceInterface $earningService)`: Earn points from POS.
  - `earnFromEcommerce(Request $request, PointEarningServiceInterface $earningService)`: Earn points from eCommerce.
  
- **Redeem Points**
  - `redeemModal(Wallet $wallet)`: Show redeem modal for a specific wallet.
  - `redeem(Request $request, Wallet $wallet, RewardServiceInterface $rewardService)`: Redeem points for a specific wallet.
  
- **Refund Points**
  - `refundModal(Wallet $wallet)`: Show refund modal for a specific wallet.
  - `refund(Request $request, PointRefundServiceInterface $refundService)`: Refund points.
  
- **Adjust Points**
  - `adjustModal(Wallet $wallet)`: Show adjust modal for a specific wallet.
  - `adjust(Request $request, Wallet $wallet, PointAdjustmentServiceInterface $adjustService)`: Adjust points for a specific wallet.
  
- **Mint Points**
  - `mintModal(Wallet $wallet)`: Show mint modal for a specific wallet.
  - `mint(Request $request, Wallet $wallet, PointAdjustmentServiceInterface $adjustService)`: Mint points for a specific wallet.
  
- **Ledger**
  - `ledger(Wallet $wallet)`: Show ledger for a specific wallet.
  
- **Available Wallets**
  - `availableWallets(Request $request, Member $member)`: List available wallets for a specific member.

### Services

Several services are used to handle different aspects of the point system:

- **TierServiceInterface**
  - `getMemberTier(Member $member)`: Get tier for a specific member.
  - `applyTierMultiplier(Member $member, float $points)`: Apply tier multiplier to points.
  - `getAllTiers()`: Get all tiers.
  - `getNextTier(Member $member)`: Get next tier for a specific member.
  
- **WalletServiceInterface**
  - `getWallet(int $ownerId, string $ownerType, int $pointTypeId, bool $isPrimary)`: Get wallet for a specific owner.
  - `getWalletByPointType(int $pointTypeId)`: Get wallet by point type.
  
- **PointEarningServiceInterface**
  - `earnPoints(Wallet $fromWallet, Wallet $toWallet, float $amount, string $source, ?string $referenceType = null, ?int $referenceId = null, ?string $description = null)`: Earn points.
  - `earnFromPurchase(Wallet $wallet, float $amount, string $channel, ?string $referenceType = null, ?int $referenceId = null, ?string $description = null)`: Earn points from purchase.
  - `issuePoints(Wallet $wallet, float $amount, string $source, ?string $description = null)`: Issue points.
  
- **RewardServiceInterface**
  - `redeemReward(Wallet $wallet, int $rewardId, ?string $referenceType = null, ?int $referenceId = null, ?string $description = null)`: Redeem reward.
  
- **PointRefundServiceInterface**
  - `refundPoints(Wallet $fromWallet, Wallet $toWallet, float $amount, string $referenceType, int $referenceId, ?string $description = null)`: Refund points.
  
- **PointAdjustmentServiceInterface**
  - `adjustPoints(Wallet $wallet, float $amount, string $reason)`: Adjust points.
  - `mintPoints(Wallet $wallet, float $amount, string $reason)`: Mint points.
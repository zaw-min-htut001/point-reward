# Campaign Management

The campaign management module allows you to create, manage, and generate reports for promotional campaigns.

## Key Components

### Routes

The following routes are defined for managing campaigns:

- `GET /loyalty/campaigns`: List all campaigns.
- `GET /loyalty/campaigns/create`: Show form to create a new campaign.
- `POST /loyalty/campaigns`: Store a new campaign.
- `GET /loyalty/campaigns/{campaign}/edit`: Show form to edit a specific campaign.
- `PUT/PATCH /loyalty/campaigns/{campaign}`: Update a specific campaign.
- `GET /loyalty/campaigns/{campaign}/confirm-delete`: Confirm deletion of a campaign.
- `DELETE /loyalty/campaigns/{campaign}`: Delete a specific campaign.
- `GET /loyalty/campaigns/{campaign}/report`: Generate report for a specific campaign.

### Controllers

The `CampaignController` is responsible for handling campaign-related operations. Here are some key methods:

- **Index**
  - `index()`: List all campaigns.
  
- **Create**
  - `create()`: Show form to create a new campaign.
  
- **Store**
  - `store(Request $request, PromoCampaignServiceInterface $campaignService)`: Store a new campaign.
  
- **Edit**
  - `edit(PromoCampaign $campaign)`: Show form to edit a specific campaign.
  
- **Update**
  - `update(Request $request, PromoCampaign $campaign)`: Update a specific campaign.
  
- **Confirm Delete**
  - `confirmDelete(PromoCampaign $campaign)`: Confirm deletion of a specific campaign.
  
- **Destroy**
  - `destroy(PromoCampaign $campaign)`: Delete a specific campaign.
  
- **Report**
  - `report(PromoCampaign $campaign, PromoCampaignServiceInterface $campaignService)`: Generate report for a specific campaign.

### Services

The `PromoCampaignServiceInterface` defines methods for handling promotional campaigns:

- **Create Campaign**
  - `createCampaign(string $name, int $pointTypeId, float $totalBudget, \DateTime $startDate, \DateTime $endDate): int`: Create a new campaign.
  
- **Issue Promo Points**
  - `issuePromoPoints(int $memberId, int $pointTypeId, float $amount, string $campaignName, ?int $campaignId = null): void`: Issue promo points to a member.
  
- **Update Campaign Status**
  - `updateCampaignStatus(int $campaignId, bool $active): void`: Update the status of a campaign.
  
- **Check Campaign Status**
  - `checkCampaignStatus(): void`: Check the status of all campaigns.
  
- **Get Campaign Report**
  - `getCampaignReport(int $campaignId): array`: Generate a report for a specific campaign.

## WORKFLOW
- **List Campaigns**:
```mermaid
graph TD
    A[Start: GET /loyalty/campaigns] --> B{Authenticate User}
    B -->|Authorized| C[Fetch PromoCampaigns]
    B -->|Unauthorized| D[Return 403]
    C --> E[Load pointType Relation]
    E --> F[Paginate Records]
    F --> G[Render Index View]
    G --> H[End]
```    
- **Show Create Campaign Form**:
```mermaid
graph TD
    A[Start: GET /loyalty/campaigns/create] --> B{Authenticate User}
    B -->|Authorized| C[Fetch PointTypes]
    B -->|Unauthorized| D[Return 403]
    C --> E[Render Create Modal View]
    E --> F[End]
```    
- **Create Campaign**:
```mermaid
graph TD
    A[Start: POST /loyalty/campaigns] --> B{Authenticate User}
    B -->|Authorized| C{Validate Request}
    B -->|Unauthorized| D[Return 403]
    C -->|Valid name, point_type_id, budget, dates| E[Call createCampaign]
    C -->|Invalid| F[Return Validation Error]
    E --> G[Redirect with Success]
    G --> H[End]
```
- **Show Edit Campaign Form**:
```mermaid
graph TD
    A[Start: GET /loyalty/campaigns/edit] --> B{Authenticate User}
    B -->|Authorized| C[Fetch Campaign]
    B -->|Unauthorized| D[Return 403]
    C --> E[Fetch PointTypes]
    E --> F[Render Edit Modal View]
    F --> G[End]
```    

- **Update Campaign**:
```mermaid
graph TD
    A[Start: PUT /loyalty/campaigns] --> B{Authenticate User}
    B -->|Authorized| C{Validate Request}
    B -->|Unauthorized| D[Return 403]
    C -->|Valid name, point_type_id, budget, dates, active| E[Update Campaign]
    C -->|Invalid| F[Return Validation Error]
    E --> G[Redirect with Success]
    G --> H[End]
```
- **Delete Campaign**:
```mermaid
graph TD
    A[Start: DELETE /loyalty/campaigns] --> B{Authenticate User}
    B -->|Authorized| C[Delete Campaign]
    B -->|Unauthorized| D[Return 403]
    C --> E[Redirect with Success]
    E --> F[End]
```    
- **View Campaign Report**:
```mermaid
graph TD
    A[Start: GET /loyalty/campaigns/report] --> B{Authenticate User}
    B -->|Authorized| C[Fetch Campaign]
    B -->|Unauthorized| D[Return 403]
    C --> E[Call getCampaignReport]
    E --> F[Render Report View]
    F --> G[End]
```    
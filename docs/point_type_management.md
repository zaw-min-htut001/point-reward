# Point Type Management

The point type management module allows you to create, manage, and single point types for the member point system.

## Key Components

### Routes

The following routes are defined for managing point types:

- `GET /loyalty/point-types`: List all point types.
- `GET /loyalty/point-types/create`: Show form to create a new point type.
- `POST /loyalty/point-types`: Store a new point type.
- `GET /loyalty/point-types/{pointType}/edit`: Show form to edit a specific point type.
- `PUT/PATCH /loyalty/point-types/{pointType}`: Update a specific point type.
- `GET /loyalty/point-types/{pointType}/confirm-delete`: Confirm deletion of a specific point type.
- `DELETE /loyalty/point-types/{pointType}`: Delete a specific point type.

### Controllers

The `PointTypeController` is responsible for handling point type-related operations. Here are some key methods:

- **Index**
  - `index()`: List all point types.
  
- **Create**
  - `create(Request $request)`: Show form to create a new point type. Ensures that only one point type is allowed.
  
- **Store**
  - `store(Request $request)`: Store a new point type. Validates input data and creates a business wallet (primary) for the point type.
  
- **Edit**
  - `edit(PointType $pointType)`: Show form to edit a specific point type.
  
- **Update**
  - `update(Request $request, PointType $pointType)`: Update a specific point type. Validates input data and updates the point type.
  
- **Confirm Delete**
  - `confirmDelete(PointType $pointType)`: Confirm deletion of a specific point type. Checks if the point type has associated wallets or transactions.
  
- **Destroy**
  - `destroy(PointType $pointType)`: Delete a specific point type. Checks if the point type has associated wallets or transactions.

### Entities

The `PointType` entity represents a point type in the system. Here are the key attributes:

- `id`: Unique identifier for the point type.
- `name`: Name of the point type.
- `is_expirable`: Boolean indicating whether points expire.
- `default_expiry_days`: Default number of days for point expiration.
- `exchange_rate`: Exchange rate for converting points to currency.

### Services

No specific services are directly related to the `PointTypeController`. However, the controller interacts with the `Wallet` entity to create a primary business wallet for the point type.

## WORKFLOW
- **Create Point Type**:
```mermaid
graph TD
    A[Start: POST /loyalty/point-types] --> B{Authenticate User}
    B -->|Authorized| C{Validate Request}
    B -->|Unauthorized| D[Return 403]
    C -->|Valid name, is_expirable, etc.| E{PointType Exists?}
    C -->|Invalid| F[Return Validation Error]
    E -->|Yes| G[Return Error: Only one allowed]
    E -->|No| H[Begin Transaction]
    H --> I[Create PointType]
    I --> J{Created Successfully?}
    J -->|Yes| K[Create Primary Business Wallet]
    J -->|No| L[Rollback Transaction]
    K --> M[Commit Transaction]
    M --> N[Return Success]
    L --> O[Return Error: Exists]
    N --> P[End]
    O --> P
```    
- **Update Point Type**:
```mermaid
graph TD
    A[Start: PUT /loyalty/point-types/pointType_id] --> B{Authenticate User}
    B -->|Authorized| C{Validate Request}
    B -->|Unauthorized| D[Return 403]
    C -->|Valid name, is_expirable, etc.| E[Begin Transaction]
    C -->|Invalid| F[Return Validation Error]
    E --> G[Update PointType]
    G --> H{Update Successful?}
    H -->|Yes| I[Commit Transaction]
    H -->|No| J[Rollback Transaction]
    I --> K[Return Success]
    J --> L[Return Error]
    K --> M[End]
    L --> M
```
- **Delete Point Type**:
```mermaid
graph TD
    A[Start: DELETE /loyalty/point-types/pointType_id] --> B{Authenticate User}
    B -->|Authorized| C{Fetch PointType}
    B -->|Unauthorized| D[Return 403]
    C --> E{Has Wallets or Transactions?}
    E -->|Yes| F[Return Error: Cannot delete]
    E -->|No| G[Begin Transaction]
    G --> H[Delete PointType]
    H --> I{Deletion Successful?}
    I -->|Yes| J[Commit Transaction]
    I -->|No| K[Rollback Transaction]
    J --> L[Return Success]
    K --> M[Return Error]
    L --> N[End]
    M --> N
```
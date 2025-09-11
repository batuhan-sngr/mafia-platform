# Mafia Platform - Microservices Architecture

## Team Composition

| Full Name        | Group   | Services                               | Language   |
|------------------|---------|----------------------------------------|------------|
| Tudor Popov      | FAF-222 | Rumours Service, Communication Service | Rust       |
| Martiniuc Artiom | FAF-222 | Task Service, Voting Service           | TS         |
| Emre Batuhan Sungur | FAF - 221| User Management Service, Game Service | GO |
| Tabanschi Nichita       | FAF-222 | Shop Service , Roleplay Service   |   TS   |
| [Member 5]       | [Group] | [Service Name]                         | [Language] |


## Project Overview
A comprehensive Mafia game platform built with microservices architecture, supporting up to 30 players per game with real-time communication, role-based gameplay, and strategic voting mechanics.

## Architecture Diagram

## Service Descriptions

### 1. User Management Service
- **Responsibility**: User profiles, authentication, currency management, device tracking
- **Technology**: GO
- **Database**: PostgreSQL
- **Key Features**:
  - User registration and authentication
  - Profile management (email, username, password, identification)
  - In-game currency tracking
  - Device and location information for single profile enforcement

### 2. Game Service (Main Component)
- **Responsibility**: Game state management, day/night cycles, lobby orchestration
- **Technology**: GO
- **Database**: Redis and PostgreSQL
- **Key Features**:
  - Day/Night cycle management
  - Player lobby management (up to 30 players)
  - Game state tracking (alive status, roles, careers)
  - Event broadcasting and notifications
  - Voting initiation and result announcements

### 3. Shop Service
- **Responsibility**: In-game item marketplace and inventory management
- **Technology**: NestJS
- **Database**: PostgreSQL
- **Key Features**:
  - Item catalog with descriptions and prices
  - Daily quantity balancing algorithm
  - Currency-based transactions
  - Protection items (garlic, water, etc.)

### 4. Roleplay Service
- **Responsibility**: Role-based actions and ability management
- **Technology**: NestJS
- **Database**: PostgreSQL
- **Key Features**:
  - Role-specific ability enforcement
  - Action logging and validation
  - Filtered announcement creation
  - Immunity and protection mechanics

### 5. Town Service
- **Responsibility**: Location and movement tracking
- **Technology**: [To be filled by team member]
- **Database**: [To be filled by team member]
- **Key Features**:
  - Location catalog (Shop, Informator Bureau, etc.)
  - User movement tracking
  - Task Service integration for location-based tasks

### 6. Character Service
- **Responsibility**: Character customization and inventory management
- **Technology**: [To be filled by team member]
- **Database**: [To be filled by team member]
- **Key Features**:
  - Character appearance customization
  - Asset management system
  - Inventory tracking from shop purchases
  - Customizable slots (hair accessories, coats, etc.)

### 7. Rumours Service
- **Responsibility**: Information marketplace and strategic intelligence
- **Technology**: Rust
- **Database**: MySQL
- **Key Features**:
  - Random information generation based on game state
  - Role-based information filtering
  - Currency-based information purchasing
  - Integration with Task Service and Character Service data
  - Information reliability and authenticity tracking

### 8. Communication Service
- **Responsibility**: Real-time messaging and chat management
- **Technology**: Rust
- **Database**: Redis (primary), PostgreSQL (persistent chat history)
- **Key Features**:
  - Global chat during voting hours
  - Private Mafia team chat
  - Location-based proximity chat
  - Real-time message delivery
  - Chat history persistence
  - Message filtering and moderation

### 9. Task Service
- **Responsibility**: Daily task assignment and completion tracking
- **Technology**: NestJS
- **Database**: PostgreSQL
- **Key Features**:
  - Role and career-based task generation
  - Task completion validation
  - Currency reward distribution
  - Integration with item usage, location visits, and player interactions

### 10. Voting Service
- **Responsibility**: Democratic elimination process management
- **Technology**: NestJS
- **Database**: MongoDB
- **Key Features**:
  - Vote collection and tallying
  - Vote history tracking
  - Result calculation and notification
  - Integration with Game Service for elimination execution

## Technology Stack & Communication Patterns

### Programming Languages
- **Rust**: Rumours Service, Communication Service
- **TS**: Task Service, Voting Service
- **TS**: Shop Service, Roleplay Service

### Database Technologies
- **MySQL**: Rumours Service (structured information storage)
- **Redis**: Communication Service (real-time messaging, session management)
- **PostgreSQL**: Communication Service (persistent chat history)
- **PostgreSQL**: Task Service (structured tasks, relational with users, transactional updates for rewards)
- **MongoDB**: Voting Service (append-only votes, flexible schema, fast aggregations for daily results)
- **PostgreSQL**: Shop Service (ACID Compliance, Complex Queries,Relational Structure,Transaction Safety)
- **PostgreSQL**: Roleplay Service (Data Consistency, Complex Relationships,Audit Trail)

### Communication Patterns
- **REST APIs**: Primary communication between services
- **WebSocket**: Real-time communication for chat and game events
- **Message Queues**: Asynchronous event processing
- **Service Discovery**: Dynamic service location and health checking

### Architecture Motivations
- **Rust for Rumours/Communication**: High performance and memory safety for real-time operations
- **MySQL for Rumours**: ACID compliance for critical game information integrity
- **Redis for Communication and Game Service**: Sub-millisecond message delivery and session management
- **PostgreSQL for Chat History**: Complex querying capabilities for chat analytics
- **NestJS + TypeScript for Task/Voting**: Strong typing, modular structure, and excellent microservices support
- **PostgreSQL for Task Service**: Relational data consistency, transactional updates for task completion and rewards
- **MongoDB for Voting Service**: Flexible schema, high write throughput, and efficient aggregation for daily vote counts
- **PostgreSQL for User Management Service and Game Service**: Relational data consistency, works well for perssint data


## Communication Contracts

### Inter-Service Data Management
- **Database Strategy**: Each service maintains its own database for independence
- **Data Consistency**: Eventual consistency model with event-driven updates
- **API Gateway**: Centralized routing and authentication

### API Endpoints
#### User Management Service Endpoints
```json
POST /api/v1/auth/register
Content-Type: application/json
{
  "username": "string",
  "email": "string",
  "password": "string",
  "device_info": {
    "device_id": "string",
    "device_type": "string",
    "os_version": "string"
  },
  "location_info": {
    "ip_address": "string",
    "country": "string",
    "region": "string"
  }
}

Response: 201 Created
{
  "user_id": "uuid",
  "username": "string",
  "email": "string",
  "access_token": "string",
  "refresh_token": "string",
  "expires_at": "timestamp"
}
POST /api/v1/auth/login
Content-Type: application/json
{
  "email": "string",
  "password": "string",
  "device_info": {
    "device_id": "string",
    "device_type": "string",
    "os_version": "string"
  }
}

Response: 200 OK
{
  "user_id": "uuid",
  "username": "string",
  "email": "string",
  "access_token": "string",
  "refresh_token": "string",
  "expires_at": "timestamp"
}
POST /api/v1/auth/refresh
Content-Type: application/json
{
  "refresh_token": "string"
}

Response: 200 OK
{
  "access_token": "string",
  "expires_at": "timestamp"
}
POST /api/v1/auth/logout
Authorization: Bearer {access_token}

Response: 200 OK
{
  "message": "Successfully logged out"
}
GET /api/v1/users/{user_id}
Authorization: Bearer {access_token}

Response: 200 OK
{
  "user_id": "uuid",
  "username": "string",
  "email": "string",
  "created_at": "timestamp",
  "last_login": "timestamp",
  "is_active": "boolean",
  "currency_balance": "integer"
}
PUT /api/v1/users/{user_id}
Authorization: Bearer {access_token}
Content-Type: application/json
{
  "username": "string",
  "email": "string"
}

Response: 200 OK
{
  "user_id": "uuid",
  "username": "string",
  "email": "string",
  "updated_at": "timestamp"
}
DELETE /api/v1/users/{user_id}
Authorization: Bearer {access_token}

Response: 200 OK
{
  "message": "User account deleted successfully"
}
GET /api/v1/users/{user_id}/currency
Authorization: Bearer {access_token}

Response: 200 OK
{
  "user_id": "uuid",
  "balance": "integer",
  "last_updated": "timestamp"
}

POST /api/v1/users/{user_id}/currency/add
Authorization: Bearer {access_token}
Content-Type: application/json
{
  "amount": "integer",
  "reason": "string",
  "transaction_id": "string"
}

Response: 200 OK
{
  "user_id": "uuid",
  "previous_balance": "integer",
  "new_balance": "integer",
  "transaction_id": "string"
}
```

```json
POST /api/v1/users/{user_id}/currency/deduct
Authorization: Bearer {access_token}
Content-Type: application/json
{
  "amount": "integer",
  "reason": "string",
  "transaction_id": "string"
}

Response: 200 OK
{
  "user_id": "uuid",
  "previous_balance": "integer",
  "new_balance": "integer",
  "transaction_id": "string"
}
GET /api/v1/users/{user_id}/devices
Authorization: Bearer {access_token}

Response: 200 OK
{
  "devices": [
    {
      "device_id": "string",
      "device_type": "string",
      "os_version": "string",
      "last_used": "timestamp",
      "is_active": "boolean"
    }
  ]
}
DELETE /api/v1/users/{user_id}/devices/{device_id}
Authorization: Bearer {access_token}

Response: 200 OK
{
  "message": "Device removed successfully"
}
```

#### Game Service Endpoints

```json
POST /api/v1/games/create
Authorization: Bearer {access_token}
Content-Type: application/json
{
  "game_name": "string",
  "max_players": "integer",
  "game_settings": {
    "day_duration": "integer",
    "night_duration": "integer",
    "roles_config": {
      "mafia_count": "integer",
      "doctor_count": "integer",
      "detective_count": "integer",
      "townspeople_count": "integer"
    }
  }
}

Response: 201 Created
{
  "game_id": "uuid",
  "game_name": "string",
  "host_id": "uuid",
  "max_players": "integer",
  "current_players": "integer",
  "status": "waiting",
  "created_at": "timestamp"
}
POST /api/v1/games/{game_id}/join
Authorization: Bearer {access_token}
Content-Type: application/json
{
  "user_id": "uuid"
}

Response: 200 OK
{
  "game_id": "uuid",
  "player_id": "uuid",
  "position": "integer",
  "status": "joined"
}
GET /api/v1/games/{game_id}
Authorization: Bearer {access_token}

Response: 200 OK
{
  "game_id": "uuid",
  "game_name": "string",
  "host_id": "uuid",
  "players": [
    {
      "player_id": "uuid",
      "username": "string",
      "is_alive": "boolean",
      "role": "string",
      "career": "string",
      "joined_at": "timestamp"
    }
  ],
  "status": "string",
  "current_phase": "string",
  "phase_ends_at": "timestamp",
  "created_at": "timestamp"
}

POST /api/v1/games/{game_id}/start
Authorization: Bearer {access_token}

Response: 200 OK
{
  "game_id": "uuid",
  "status": "in_progress",
  "current_phase": "day",
  "phase_ends_at": "timestamp",
  "message": "Game started successfully"
}
ET /api/v1/games/{game_id}/state
Authorization: Bearer {access_token}

Response: 200 OK
{
  "game_id": "uuid",
  "current_phase": "string",
  "phase_number": "integer",
  "phase_ends_at": "timestamp",
  "alive_players": "integer",
  "total_players": "integer",
  "last_events": [
    {
      "event_id": "uuid",
      "event_type": "string",
      "message": "string",
      "timestamp": "timestamp"
    }
  ]
}

POST /api/v1/games/{game_id}/events
Authorization: Bearer {access_token}
Content-Type: application/json
{
  "event_type": "string",
  "message": "string",
  "affected_players": ["uuid"],
  "metadata": "object"
}

Response: 201 Created
{
  "event_id": "uuid",
  "game_id": "uuid",
  "event_type": "string",
  "message": "string",
  "timestamp": "timestamp"
}
```
#### Rumours Service Endpoints
```json
GET /rumours/available/{user_id}
Response: {
  "rumours": [
    {
      "id": "rumour_123",
      "category": "player_behavior",
      "cost": 50,
      "reliability": 0.8,
      "preview": "A player was seen visiting suspicious locations..."
    }
  ]
}

POST /rumours/purchase
Request: {
  "user_id": "user_123",
  "rumour_id": "rumour_456"
}
Response: {
  "success": true,
  "rumour": {
    "content": "Player John_Doe was seen entering the Mayor's office at 3 AM",
    "evidence": ["location_logs", "timestamp_data"],
    "reliability": 0.8
  },
  "remaining_currency": 450
}

GET /rumours/history/{user_id}
Response: {
  "purchased_rumours": [
    {
      "rumour_id": "rumour_456",
      "purchase_date": "2025-09-11T14:30:00Z",
      "cost": 50,
      "accuracy_revealed": true
    }
  ]
}
```

#### Communication Service Endpoints
```json
POST /chat/send
Request: {
  "user_id": "user_123",
  "channel_type": "global|mafia|location",
  "channel_id": "voting_room",
  "message": "I think the mayor is suspicious",
  "timestamp": "2025-09-11T14:30:00Z"
}
Response: {
  "message_id": "msg_789",
  "delivered": true,
  "recipients_count": 15
}

GET /chat/history
Query: ?channel_id=voting_room&limit=50&before=msg_789
Response: {
  "messages": [
    {
      "message_id": "msg_788",
      "user_id": "user_456",
      "username": "detective_jane",
      "message": "The evidence points to multiple suspects",
      "timestamp": "2025-09-11T14:25:00Z",
      "edited": false
    }
  ]
}

POST /chat/channels/create
Request: {
  "type": "private",
  "name": "Mafia Strategy Room",
  "participants": ["user_123", "user_456", "user_789"],
  "game_id": "game_001"
}
Response: {
  "channel_id": "channel_private_001",
  "created": true,
  "invite_codes": ["inv_abc123", "inv_def456"]
}

WebSocket: /ws/chat/{user_id}
Events: {
  "new_message": { ... },
  "user_joined": { ... },
  "user_left": { ... },
  "channel_closed": { ... }
}
```

#### Task Service Endpoints
```json
POST /tasks/generate
Request: {
  "game_id": "game_001",
  "player_ids": ["player_123", "player_456"],
  "day_number": 1
}
Response: {
  "generated_tasks": [...]
}

PATCH /tasks/{task_id}/complete
Request: {
  "player_id": "player_123",
  "used_items": ["garlic"],
  "completion_notes": "Completed task successfully"
}
Response: {
  "task_id": "task_789",
  "status": "COMPLETED",
  "completed_at": "2025-09-11T18:00:00Z",
  "currency_awarded": 20
}

GET /tasks/player/{player_id}
Response: {
  "tasks": [...]
}
```

#### Voting Service Endpoints
```json
POST /voting/sessions
Request: {
  "game_id": "game_001",
  "eligible_voters": ["player_123", "player_456"],
  "eligible_targets": ["player_234", "player_567"],
  "duration_minutes": 10,
  "day_number": 1
}
Response: {
  "session_id": "session_001",
  "status": "ACTIVE",
  "start_time": "...",
  "end_time": "...",
  "total_votes": 0
}

POST /voting/vote
Request: {
  "session_id": "session_001",
  "voter_id": "player_123",
  "target_player_id": "player_567"
}
Response: {
  "vote_id": "vote_123",
  "is_valid": true,
  "cast_at": "2025-09-11T20:01:00Z"
}

GET /voting/sessions/{game_id}/current
Response: {
  "session_id": "session_001",
  "status": "ACTIVE",
  "total_votes": 8,
  "eligible_voters": [...],
  "eligible_targets": [...]
}

PATCH /voting/sessions/{session_id}/end
Response: {
  "session_id": "session_001",
  "status": "ENDED",
  "eliminated_player_id": "player_234"
}
```

#### Cross-Service Integration Points
```json
// From Game Service to Communication Service
POST /chat/game-events
Request: {
  "event_type": "player_eliminated",
  "game_id": "game_001",
  "message": "John_Doe has been voted out by the town",
  "affected_channels": ["global", "spectator"]
}

// From Rumours Service to multiple services
GET /task-service/player-activities/{user_id}
GET /character-service/appearance/{user_id}
GET /town-service/movement-logs/{user_id}
```
#### Shop Service Endpoints
```json
GET /shop/items
Response: {
  "categories": {
    "protection": [
      {
        "item_id": "garlic_001",
        "name": "Garlic Clove",
        "description": "Protects against vampire attacks",
        "price": 50,
        "daily_limit": 3,
        "remaining_stock": 2,
        "effects": ["vampire_immunity"]
      }
    ],
    "utilities": [
      {
        "item_id": "water_001",
        "name": "Holy Water",
        "description": "Extinguishes arsonist fires",
        "price": 75,
        "daily_limit": 2,
        "remaining_stock": 1,
        "effects": ["fire_protection", "arsonist_counter"]
      }
    ]
  }
}

POST /shop/purchase
Request: {
  "player_id": "player_123",
  "game_id": "game_001",
  "items": [
    {
      "item_id": "garlic_001",
      "quantity": 1
    }
  ]
}
Response: {
  "transaction_id": "txn_456",
  "status": "SUCCESS",
  "total_cost": 50,
  "remaining_balance": 200,
  "items_purchased": [
    {
      "item_id": "garlic_001",
      "quantity": 1,
      "expires_at": "2025-09-12T06:00:00Z"
    }
  ]
}

GET /shop/inventory/{player_id}
Response: {
  "player_id": "player_123",
  "items": [
    {
      "inventory_id": "inv_789",
      "item_id": "garlic_001",
      "quantity": 1,
      "acquired_at": "2025-09-11T20:15:00Z",
      "expires_at": "2025-09-12T06:00:00Z",
      "status": "ACTIVE"
    }
  ],
  "currency_balance": 200
}

POST /shop/use-item
Request: {
  "player_id": "player_123",
  "inventory_id": "inv_789",
  "context": "night_defense"
}
Response: {
  "usage_id": "use_101",
  "item_consumed": true,
  "effects_applied": ["vampire_immunity"],
  "duration": "until_dawn",
  "remaining_quantity": 0
}

GET /shop/balance/{player_id}
Response: {
  "player_id": "player_123",
  "current_balance": 200,
  "pending_transactions": 0,
  "daily_earned": 50,
  "daily_spent": 125
}
```

#### Roleplay Service Endpoints
```json
POST /roleplay/night-action
Request: {
  "game_id": "game_001",
  "player_id": "player_123",
  "action_type": "MURDER",
  "target_player_id": "player_456",
  "night_number": 1
}
Response: {
  "action_id": "action_789",
  "status": "QUEUED",
  "can_execute": true,
  "scheduled_for": "night_phase_end",
  "warnings": []
}

POST /roleplay/day-action
Request: {
  "game_id": "game_001",
  "player_id": "player_234",
  "action_type": "INVESTIGATE",
  "target_player_id": "player_567",
  "day_number": 2
}
Response: {
  "action_id": "action_890",
  "status": "EXECUTED",
  "result": {
    "target_role": "CIVILIAN",
    "confidence": 0.85,
    "modifiers_applied": ["sheriff_accuracy_bonus"]
  }
}

GET /roleplay/abilities/{player_id}
Response: {
  "player_id": "player_123",
  "role": "MAFIA",
  "abilities": [
    {
      "ability_id": "murder",
      "name": "Nightkill",
      "description": "Eliminate a player during night phase",
      "cooldown_remaining": 0,
      "uses_remaining": "unlimited",
      "restrictions": ["night_only", "living_targets_only"]
    }
  ],
  "passive_effects": ["mafia_chat_access", "immunity_to_self_votes"]
}

POST /roleplay/validate-action
Request: {
  "player_id": "player_123",
  "action_type": "MURDER",
  "target_player_id": "player_456",
  "game_phase": "NIGHT"
}
Response: {
  "is_valid": false,
  "blocking_factors": [
    {
      "type": "TARGET_IMMUNITY",
      "source": "garlic_protection",
      "description": "Target has vampire immunity from garlic"
    }
  ],
  "suggested_alternatives": ["investigate", "wait"]
}

GET /roleplay/action-history/{game_id}
Response: {
  "game_id": "game_001",
  "actions": [
    {
      "action_id": "action_789",
      "night_number": 1,
      "actor_role": "MAFIA",
      "action_type": "MURDER_ATTEMPT",
      "target_role": "CIVILIAN",
      "result": "BLOCKED",
      "blocking_reason": "garlic_immunity",
      "timestamp": "2025-09-11T23:59:00Z"
    }
  ]
}

POST /roleplay/generate-announcement
Request: {
  "game_id": "game_001",
  "phase_results": [
    {
      "action_type": "MURDER",
      "success": false,
      "target_id": "player_456",
      "blocking_factor": "protection_item"
    }
  ]
}
Response: {
  "announcement_id": "ann_123",
  "filtered_message": "The night was quiet. No one was eliminated.",
  "private_details": {
    "mafia_perspective": "Your attack on Player456 was blocked by their garlic protection."
  },
  "broadcast_ready": true
}
```


## GitHub Workflow

### Repository Structure
- **Main Repository**: `mafia-platform` (Public)
  - Contains architecture documentation
  - Submodules for each microservice
  - Docker Compose configuration
  - CI/CD pipeline definitions

- **Private Submodules**:
  - `PAD-Rumors-Service`
  - `PAD-Communication-Service`
  - `PAD-Task-Service`
  - `PAD-Voting-Service`
  - `PAD-Shop-Service`

### Branching Strategy
- **Main Branch**: Production-ready code only
- **Development Branch**: Integration branch for features
- **Feature Branches**: `feature/add-something`
- **Hotfix Branches**: `hotfix/critical-issue-fix`

### Merge Requirements
- **Minimum 2 approvals** required for all pull requests
- **All CI checks must pass** before merging to main branch
- **Code coverage minimum: 80%** across all services
- **All tests must pass** before merge approval

### Naming Conventions
- **Branches**: `feature/add-something`
- **Commits**: `feat: something was added`
- **Pull Requests**: Clear descriptive titles matching the feature

### Pull Request Content Requirements
- Clear description of changes and their purpose
- Test coverage report showing 80% minimum coverage
- List of any breaking changes or API modifications
- Screenshots or examples for UI-related changes
- Migration instructions if database changes are involved

### Versioning Strategy
- **Git Tags**: Used for version releases (e.g., `v1.0.0`, `v1.1.0`)
- **Semantic Versioning**: Following SemVer principles
- **Service-specific versioning**: Each submodule maintains its own version tags

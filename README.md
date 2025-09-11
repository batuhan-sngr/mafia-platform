# Mafia Platform - Microservices Architecture

## Team Composition

| Full Name        | Group   | Services                               | Language   |
|------------------|---------|----------------------------------------|------------|
| Tudor Popov      | FAF-222 | Rumours Service, Communication Service | Rust       |
| Martiniuc Artiom | FAF-222 | Task Service, Voting Service           | TS         |
| [Member 3]       | [Group] | [Service Name]                         | [Language] |
| [Member 4]       | [Group] | [Service Name]                         | [Language] |
| [Member 5]       | [Group] | [Service Name]                         | [Language] |


## Project Overview
A comprehensive Mafia game platform built with microservices architecture, supporting up to 30 players per game with real-time communication, role-based gameplay, and strategic voting mechanics.

## Architecture Diagram

## Service Descriptions

### 1. User Management Service
- **Responsibility**: User profiles, authentication, currency management, device tracking
- **Technology**: [To be filled by team member]
- **Database**: [To be filled by team member]
- **Key Features**:
  - User registration and authentication
  - Profile management (email, username, password, identification)
  - In-game currency tracking
  - Device and location information for single profile enforcement

### 2. Game Service (Main Component)
- **Responsibility**: Game state management, day/night cycles, lobby orchestration
- **Technology**: [To be filled by team member]
- **Database**: [To be filled by team member]
- **Key Features**:
  - Day/Night cycle management
  - Player lobby management (up to 30 players)
  - Game state tracking (alive status, roles, careers)
  - Event broadcasting and notifications
  - Voting initiation and result announcements

### 3. Shop Service
- **Responsibility**: In-game item marketplace and inventory management
- **Technology**: [To be filled by team member]
- **Database**: [To be filled by team member]
- **Key Features**:
  - Item catalog with descriptions and prices
  - Daily quantity balancing algorithm
  - Currency-based transactions
  - Protection items (garlic, water, etc.)

### 4. Roleplay Service
- **Responsibility**: Role-based actions and ability management
- **Technology**: [To be filled by team member]
- **Database**: [To be filled by team member]
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
- **[Language 3]**: [Services]

### Database Technologies
- **MySQL**: Rumours Service (structured information storage)
- **Redis**: Communication Service (real-time messaging, session management)
- **PostgreSQL**: Communication Service (persistent chat history)
- **PostgreSQL**: Task Service (structured tasks, relational with users, transactional updates for rewards)
- **MongoDB**: Voting Service (append-only votes, flexible schema, fast aggregations for daily results)
- **[Additional databases as chosen by other team members]**

### Communication Patterns
- **REST APIs**: Primary communication between services
- **WebSocket**: Real-time communication for chat and game events
- **Message Queues**: Asynchronous event processing
- **Service Discovery**: Dynamic service location and health checking

### Architecture Motivations
- **Rust for Rumours/Communication**: High performance and memory safety for real-time operations
- **MySQL for Rumours**: ACID compliance for critical game information integrity
- **Redis for Communication**: Sub-millisecond message delivery and session management
- **PostgreSQL for Chat History**: Complex querying capabilities for chat analytics
- **NestJS + TypeScript for Task/Voting**: Strong typing, modular structure, and excellent microservices support
- **PostgreSQL for Task Service**: Relational data consistency, transactional updates for task completion and rewards
- **MongoDB for Voting Service**: Flexible schema, high write throughput, and efficient aggregation for daily vote counts

## Communication Contracts

### Inter-Service Data Management
- **Database Strategy**: Each service maintains its own database for independence
- **Data Consistency**: Eventual consistency model with event-driven updates
- **API Gateway**: Centralized routing and authentication

### API Endpoints

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
  - `[other-service-repositories]`

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

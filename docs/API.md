# API Documentation

Base URL: `http://localhost:5000/api`

## Authentication

All endpoints (except login/register) require a JWT token in the Authorization header:

```
Authorization: Bearer <your_jwt_token>
```

## Endpoints

### Auth

#### Register User
```
POST /auth/register
Content-Type: application/json

{
  "email": "user@example.com",
  "username": "username",
  "password": "password123",
  "first_name": "John",
  "last_name": "Doe"
}

Response: 201 Created
{
  "id": "user_id",
  "email": "user@example.com",
  "token": "jwt_token"
}
```

#### Login
```
POST /auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "password123"
}

Response: 200 OK
{
  "id": "user_id",
  "email": "user@example.com",
  "token": "jwt_token"
}
```

### Games

#### Get All Games
```
GET /games?status=live&limit=10&offset=0

Response: 200 OK
{
  "data": [
    {
      "id": "game_id",
      "name": "Virtual Football Match",
      "game_type": "football",
      "status": "live",
      "start_time": "2024-01-01T10:00:00Z",
      "odds": {
        "home_win": 1.8,
        "draw": 2.5,
        "away_win": 2.2
      }
    }
  ],
  "total": 100,
  "limit": 10,
  "offset": 0
}
```

#### Get Game by ID
```
GET /games/{game_id}

Response: 200 OK
{
  "id": "game_id",
  "name": "Virtual Football Match",
  "game_type": "football",
  "status": "live",
  "start_time": "2024-01-01T10:00:00Z",
  "end_time": null,
  "odds": {...},
  "result": null
}
```

### Bets

#### Place Bet
```
POST /bets
Authorization: Bearer <token>
Content-Type: application/json

{
  "game_id": "game_id",
  "bet_amount": 100.00,
  "prediction": {
    "outcome": "home_win"
  }
}

Response: 201 Created
{
  "id": "bet_id",
  "user_id": "user_id",
  "game_id": "game_id",
  "bet_amount": 100.00,
  "odds": 1.8,
  "potential_winnings": 180.00,
  "status": "pending",
  "placed_at": "2024-01-01T10:05:00Z"
}
```

#### Get User Bets
```
GET /bets?status=pending&limit=20

Response: 200 OK
{
  "data": [...],
  "total": 50,
  "limit": 20
}
```

#### Get Bet Details
```
GET /bets/{bet_id}

Response: 200 OK
{
  "id": "bet_id",
  "user_id": "user_id",
  "game_id": "game_id",
  "bet_amount": 100.00,
  "odds": 1.8,
  "potential_winnings": 180.00,
  "status": "won",
  "placed_at": "2024-01-01T10:05:00Z",
  "settled_at": "2024-01-01T11:00:00Z"
}
```

### Account

#### Get User Profile
```
GET /account/profile
Authorization: Bearer <token>

Response: 200 OK
{
  "id": "user_id",
  "email": "user@example.com",
  "username": "username",
  "first_name": "John",
  "last_name": "Doe",
  "balance": 1500.00,
  "status": "active"
}
```

#### Update Profile
```
PUT /account/profile
Authorization: Bearer <token>
Content-Type: application/json

{
  "first_name": "Jane",
  "phone": "+234XXXXXXXXXX"
}

Response: 200 OK
```

### Transactions

#### Get Transactions
```
GET /transactions?type=deposit&limit=10
Authorization: Bearer <token>

Response: 200 OK
{
  "data": [
    {
      "id": "transaction_id",
      "type": "deposit",
      "amount": 500.00,
      "status": "completed",
      "created_at": "2024-01-01T09:00:00Z"
    }
  ],
  "total": 25,
  "limit": 10
}
```

#### Initiate Deposit
```
POST /transactions/deposit
Authorization: Bearer <token>
Content-Type: application/json

{
  "amount": 500.00,
  "payment_method": "card"
}

Response: 201 Created
{
  "id": "transaction_id",
  "type": "deposit",
  "amount": 500.00,
  "status": "pending",
  "payment_url": "https://payment-gateway.com/pay/..."
}
```

### Support

#### Create Ticket
```
POST /support/tickets
Authorization: Bearer <token>
Content-Type: application/json

{
  "subject": "Withdrawal Issue",
  "description": "I cannot withdraw my winnings",
  "category": "payment",
  "priority": "high"
}

Response: 201 Created
{
  "id": "ticket_id",
  "subject": "Withdrawal Issue",
  "status": "open",
  "created_at": "2024-01-01T10:30:00Z"
}
```

#### Get Tickets
```
GET /support/tickets?status=open
Authorization: Bearer <token>

Response: 200 OK
{
  "data": [...],
  "total": 5
}
```

#### Send Message
```
POST /support/tickets/{ticket_id}/messages
Authorization: Bearer <token>
Content-Type: application/json

{
  "message": "I have tried logging out and back in"
}

Response: 201 Created
{
  "id": "message_id",
  "ticket_id": "ticket_id",
  "message": "I have tried logging out and back in",
  "created_at": "2024-01-01T10:35:00Z"
}
```

## Error Responses

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input",
    "details": [
      {
        "field": "email",
        "message": "Invalid email format"
      }
    ]
  }
}
```

## Rate Limiting

- Requests are limited to 100 per minute per IP
- Headers: `X-RateLimit-Limit`, `X-RateLimit-Remaining`

## WebSocket (Real-time Updates)

Connect to `ws://localhost:5000` with authentication token:

```javascript
const ws = new WebSocket('ws://localhost:5000?token=<jwt_token>');

ws.onmessage = (event) => {
  const data = JSON.parse(event.data);
  // Handle: game updates, odds changes, bet results
};
```

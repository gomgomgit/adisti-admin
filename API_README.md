# API Integration Documentation

## Environment Variables

Create a `.env` file in the root directory and add:

```env
VITE_SERVER_ADDRESS=http://localhost:3000
```

Change the server address to match your API server.

## API Endpoints

The application expects the following API endpoints:

### 1. Get All Conversations
- **URL:** `GET {{server_address}}/api/v1/conversations`
- **Response format:**
```json
[
  {
    "id": 1,
    "name": "John Doe",
    "initials": "JD",
    "lastMessage": "Hello, how can I help you?",
    "timestamp": "2 hours ago",
    "messages": [
      {
        "id": 1,
        "sender": "user",
        "text": "Hello, I need help",
        "timestamp": "10:30 AM"
      },
      {
        "id": 2,
        "sender": "assistant",
        "text": "I'm here to help!",
        "timestamp": "10:31 AM"
      }
    ]
  }
]
```

### 2. Delete Conversation
- **URL:** `DELETE {{server_address}}/api/v1/conversations/{id}`
- **Response:** `200 OK`

### 3. Send Message
- **URL:** `POST {{server_address}}/api/v1/conversations/{id}/messages`
- **Body:**
```json
{
  "text": "Message content",
  "sender": "user"
}
```
- **Response:** `200 OK`

## Features

1. **Auto-fetch on mount**: Conversations are automatically loaded when the component mounts
2. **Error handling**: If API fails, fallback to sample data
3. **Loading states**: Shows loading spinner while fetching data
4. **Refresh button**: Manual refresh of conversations
5. **Real-time updates**: Messages are sent to API and updated locally

## Usage

1. Set your API server address in `.env`
2. Make sure your API server is running
3. The dashboard will automatically connect and fetch conversations
4. If API is unavailable, it will show sample data as fallback

## Environment Variables

- `VITE_SERVER_ADDRESS`: The base URL of your API server (default: http://localhost:3000)

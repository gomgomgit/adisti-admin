# Adisty Admin

## Description
A conversation dashboard application built with Vue.js for managing and viewing conversation history.

## Development Guide

### Prerequisites
Before running this project, make sure you have the following installed:
- **Git** - Version control system
- **Node.js** - JavaScript runtime (v16 or higher recommended)
- **npm** - Node package manager (comes with Node.js)

### Getting Started

Follow these steps to run the project locally:

#### 1. Clone the Repository
```bash
git clone <repository-url>
cd adisty-admin
```

#### 2. Environment Configuration
```bash
# Copy the environment example file
cp .env.example .env

# Edit the .env file and fill in the required variables
# Example:
# VITE_API_URL=https://your-api-url.com
```

#### 3. Install Dependencies
```bash
npm install
```

#### 4. Run the Development Server
```bash
npm run dev
```

#### 5. Access the Application
Open your browser and navigate to: **http://localhost:5173**

> **Note**: The default port may vary if 5173 is already in use. Check the terminal output for the actual port number.

## Features
- 📋 View conversation list with timestamp formatting
- 🗑️ Delete conversations with confirmation modal
- 🔄 Auto-refresh functionality
- 🎨 Modern UI with Tailwind CSS

## Technology Stack
- **Frontend**: Vue.js 3 (Composition API)
- **Build Tool**: Vite
- **Styling**: Tailwind CSS
- **HTTP Client**: Axios

## Project Structure
```
src/
├── components/
│   └── ConversationDashboard.vue
├── assets/
├── main.js
└── App.vue
```

## Available Scripts
- `npm run dev` - Start development server
- `npm run build` - Build for production
- `npm run preview` - Preview production build
<template>
  <div class="h-screen w-screen bg-gray-50 flex flex-col">
    <!-- Header -->
    <div class="flex-none p-6 border-b border-gray-200 bg-white">
      <div class="flex items-center justify-between">
        <div>
          <h1 class="text-2xl font-semibold text-gray-800">Conversation Dashboard</h1>
          <p class="text-gray-600">Adisty Conversation History</p>
        </div>
        <div class="text-right">
          <p class="text-sm text-gray-600">Total Conversations</p>
          <p class="text-lg font-semibold text-gray-800">{{ conversations.length }}</p>
        </div>
      </div>
    </div>

    <!-- Main Content - 2 Panels -->
    <div class="flex-1 flex overflow-hidden">
      <!-- Left Panel - Conversation List -->
      <div class="w-1/2 border-r border-gray-200 bg-white flex flex-col">
        <div class="p-4 border-b border-gray-200">
          <div class="flex items-center justify-between">
            <h2 class="text-lg font-semibold text-gray-800">Conversation List</h2>
            <button 
              @click="refreshConversations"
              class="text-gray-600 hover:text-gray-800 hover:bg-gray-100 rounded-full transition-colors"
              title="Refresh conversations"
            >
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"></path>
              </svg>
            </button>
          </div>
          
          <!-- Error Message -->
          <div v-if="error" class="mt-2 p-2 bg-red-100 border border-red-400 text-red-700 rounded">
            {{ error }}
          </div>
        </div>

        <!-- Loading State -->
        <div v-if="loading" class="flex-1 flex items-center justify-center">
          <div class="text-center">
            <div class="animate-spin rounded-full h-8 w-8 border-b-2 border-blue-600 mx-auto mb-2"></div>
            <p class="text-gray-500">Loading conversations...</p>
          </div>
        </div>

        <!-- Conversation List -->
        <div v-else class="flex-1 overflow-y-auto">
          <div v-if="conversations.length === 0" class="flex items-center justify-center h-full">
            <div class="text-center">
              <svg class="mx-auto h-16 w-16 text-gray-400 mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20 13V6a2 2 0 00-2-2H6a2 2 0 00-2 2v7m16 0v5a2 2 0 01-2 2H6a2 2 0 01-2-2v-5m16 0h-2.586a1 1 0 00-.707.293l-2.414 2.414a1 1 0 01-.707.293h-3.172a1 1 0 01-.707-.293l-2.414-2.414A1 1 0 006.586 13H4"></path>
              </svg>
              <p class="text-gray-500 text-lg">No conversations found</p>
            </div>
          </div>
          
          <div v-else class="divide-y divide-gray-200">
            <div 
              v-for="conversation in conversations" 
              :key="conversation" 
              @click="showConversation(conversation)"
              class="p-4 hover:bg-gray-50 transition-colors cursor-pointer"
              :class="{ 'bg-blue-50 border-l-4 border-l-blue-500': selectedConversation?.id === conversation }"
            >
              <div class="flex items-center justify-between">
                <div class="flex items-center flex-1">
                  <div class="flex-shrink-0 h-10 w-10">
                    <div 
                      class="h-10 w-10 rounded-full flex items-center justify-center"
                      :class="selectedConversation?.id === conversation ? 'bg-blue-500' : 'bg-gray-300'"
                    >
                      <svg 
                        class="w-5 h-5" 
                        :class="selectedConversation?.id === conversation ? 'text-white' : 'text-gray-600'"
                        fill="none" 
                        stroke="currentColor" 
                        viewBox="0 0 24 24"
                      >
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 12h.01M12 12h.01M16 12h.01M21 12c0 4.418-4.03 8-9 8a9.863 9.863 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z"></path>
                      </svg>
                    </div>
                  </div>
                  <div class="ml-4 flex-1">
                    <div class="text-sm font-medium text-gray-900">{{ conversation }}</div>
                  </div>
                </div>
                <div class="flex items-center space-x-2">
                  <button 
                    @click.stop="showDeleteConfirmation(conversation)" 
                    class="p-2 text-red-600 hover:text-red-800 hover:bg-red-100 rounded-full transition-colors"
                    title="Delete conversation"
                  >
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"></path>
                    </svg>
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Right Panel - Chat History -->
      <div class="w-1/2 bg-white flex flex-col">
        <div class="p-4 border-b border-gray-200">
          <h2 class="text-lg font-semibold text-gray-800">
            {{ selectedConversation ? `${selectedConversation.name}` : 'Select a conversation' }}
          </h2>
        </div>

        <div class="flex-1 overflow-y-auto p-4">
          <div v-if="chatLoading" class="flex items-center justify-center h-full">
            <div class="text-center">
              <div class="animate-spin rounded-full h-8 w-8 border-b-2 border-blue-600 mx-auto mb-2"></div>
              <p class="text-gray-500">Loading conversation...</p>
            </div>
          </div>
          
          <div v-else-if="!selectedConversation" class="flex items-center justify-center h-full">
            <div class="text-center">
              <svg class="mx-auto h-16 w-16 text-gray-400 mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 12h.01M12 12h.01M16 12h.01M21 12c0 4.418-4.03 8-9 8a9.863 9.863 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z"></path>
              </svg>
              <p class="text-gray-500 text-lg">Select a conversation to view chat history</p>
            </div>
          </div>

          <div v-else class="space-y-4">
            <div 
              v-for="message in selectedConversation.messages" 
              :key="message.id"
              class="flex"
              :class="message.sender === 'user' ? 'justify-end' : 'justify-start'"
            >
              <div 
                class="max-w-xs lg:max-w-md px-4 py-2 rounded-lg"
                :class="message.sender === 'user' ? 'bg-blue-500 text-white' : 'bg-gray-200 text-gray-800'"
              >
                <p class="text-sm">{{ message.text }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    
    <!-- Delete Confirmation Modal -->
    <div v-if="showDeleteModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
      <div class="bg-white rounded-lg p-6 shadow-xl max-w-md w-full mx-4">
        <div class="flex items-center mb-4">
          <div class="flex-shrink-0">
            <svg class="h-10 w-10 text-red-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.5L13.732 4c-.77-.833-1.962-.833-2.732 0L3.732 16.5c-.77.833.192 2.5 1.732 2.5z"></path>
            </svg>
          </div>
          <div class="ml-3">
            <h3 class="text-lg font-medium text-gray-900">Delete Conversation</h3>
            <p class="text-sm text-gray-500">This action cannot be undone.</p>
          </div>
        </div>
        
        <div class="mb-6">
          <p class="text-gray-700">
            Are you sure you want to delete conversation <strong>{{ conversationToDelete }}</strong>? 
            This will permanently remove all messages and cannot be recovered.
          </p>
        </div>
        
        <div class="flex justify-end space-x-3">
          <button 
            @click="cancelDelete"
            class="px-4 py-2 text-sm font-medium text-gray-700 bg-gray-100 hover:bg-gray-200 rounded-md transition-colors"
          >
            Cancel
          </button>
          <button 
            @click="confirmDelete"
            class="px-4 py-2 text-sm font-medium text-white bg-red-600 hover:bg-red-700 rounded-md transition-colors"
          >
            Delete Conversation
          </button>
        </div>
      </div>
    </div>
    
    <!-- Delete Loading Overlay -->
    <div v-if="deleteLoading" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
      <div class="bg-white rounded-lg p-6 shadow-xl">
        <div class="flex items-center space-x-3">
          <div class="animate-spin rounded-full h-8 w-8 border-b-2 border-red-600"></div>
          <div>
            <p class="text-lg font-medium text-gray-900">Deleting conversation...</p>
            <p class="text-sm text-gray-600">Please wait while we delete the conversation</p>
          </div>
        </div>
      </div>
    </div>

    <!-- Notification Toast -->
    <div v-if="showNotification" class="fixed top-4 right-4 z-50 transform transition-all duration-300">
      <div 
        class="bg-white rounded-lg shadow-lg border-l-4 p-4 max-w-sm"
        :class="{
          'border-l-green-500': notification.type === 'success',
          'border-l-red-500': notification.type === 'error',
          'border-l-yellow-500': notification.type === 'warning'
        }"
      >
        <div class="flex items-start">
          <div class="flex-shrink-0">
            <!-- Success Icon -->
            <svg 
              v-if="notification.type === 'success'" 
              class="h-6 w-6 text-green-500" 
              fill="none" 
              stroke="currentColor" 
              viewBox="0 0 24 24"
            >
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
            </svg>
            <!-- Error Icon -->
            <svg 
              v-else-if="notification.type === 'error'" 
              class="h-6 w-6 text-red-500" 
              fill="none" 
              stroke="currentColor" 
              viewBox="0 0 24 24"
            >
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
            </svg>
            <!-- Warning Icon -->
            <svg 
              v-else-if="notification.type === 'warning'" 
              class="h-6 w-6 text-yellow-500" 
              fill="none" 
              stroke="currentColor" 
              viewBox="0 0 24 24"
            >
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.5L13.732 4c-.77-.833-1.962-.833-2.732 0L3.732 16.5c-.77.833.192 2.5 1.732 2.5z"></path>
            </svg>
          </div>
          <div class="ml-3 flex-1">
            <h3 
              class="text-sm font-medium"
              :class="{
                'text-green-800': notification.type === 'success',
                'text-red-800': notification.type === 'error',
                'text-yellow-800': notification.type === 'warning'
              }"
            >
              {{ notification.title }}
            </h3>
            <p class="mt-1 text-sm text-gray-600">{{ notification.message }}</p>
          </div>
          <div class="ml-4 flex-shrink-0">
            <button 
              @click="hideNotification"
              class="inline-flex text-gray-400 hover:text-gray-600 focus:outline-none"
            >
              <svg class="h-5 w-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
              </svg>
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const selectedConversation = ref(null)
const newMessage = ref('')
const conversations = ref([])
const loading = ref(false)
const chatLoading = ref(false)
const deleteLoading = ref(false)
const showDeleteModal = ref(false)
const conversationToDelete = ref(null)
const error = ref(null)

// Notification state
const showNotification = ref(false)
const notification = ref({
  type: 'success', // 'success', 'error', 'warning'
  title: '',
  message: ''
})

// API configuration
const serverAddress = import.meta.env.VITE_API_URL || ''
console.log('Server address:', serverAddress)

// Create axios instance with proxy configuration
const apiClient = axios.create({
  baseURL: serverAddress,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
    'Accept': 'application/json',
  },
  withCredentials: false,
})

// Notification functions
const showNotificationMessage = (type, title, message) => {
  notification.value = { type, title, message }
  showNotification.value = true
  
  // Auto hide after 5 seconds
  setTimeout(() => {
    showNotification.value = false
  }, 5000)
}

const hideNotification = () => {
  showNotification.value = false
}

// Fetch conversations from API
const fetchConversations = async () => {
  loading.value = true
  error.value = null
  
  try {
    const response = await apiClient.get('/api/v1/conversations')

    console.log('Conversations fetched:', response.data.conversation_ids)
    
    if (response.data && Array.isArray(response.data.conversation_ids)) {
      conversations.value = response.data.conversation_ids

      console.log('Conversations set:', conversations.value)
      console.log(conversations.value.length)
    } else {
      console.warn('Invalid response format, using fallback data')
      throw new Error('Invalid response format')
    }
    
  } catch (err) {
    console.error('Error fetching conversations:', err)
    error.value = 'Failed to fetch conversations'
    console.log('Using fallback data due to error:', err.message)

    // Fallback to sample data if API fails
    conversations.value = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
  } finally {
    loading.value = false
  }
}

const showConversation = async (conversationId) => {
  chatLoading.value = true
  error.value = null
  
  try {
    console.log('Fetching conversation details for ID:', conversationId)
    
    const response = await apiClient.get(`/api/v1/conversations/${conversationId}`)
    
    if (response.data && response.data.conversation_id) {
      const formattedMessages = response.data.messages.map((message, index) => ({
        id: index + 1,
        sender: message.role === 'human' ? 'user' : 'assistant',
        text: message.content,
        timestamp: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
      }))
      
      selectedConversation.value = {
        id: response.data.conversation_id,
        name: `Conversation ${response.data.conversation_id}`,
        messages: formattedMessages
      }
      
      console.log('Formatted conversation:', selectedConversation.value)
    } else {
      throw new Error('Invalid conversation response format')
    }
    
  } catch (err) {
    console.error('Error fetching conversation details:', err)
    
    if (err.response?.status === 404) {
      error.value = 'Conversation not found'
    } else if (err.response?.status >= 500) {
      error.value = 'Server error while fetching conversation'
    } else {
      error.value = 'Failed to fetch conversation details'
    }
    
    // Fallback to mock conversation data
    selectedConversation.value = {
      id: conversationId,
      name: `Conversation ${conversationId}`,
      messages: [
        {
          id: 1,
          sender: 'user',
          text: 'Hello, I need help with my account',
          timestamp: '10:30 AM'
        },
        {
          id: 2,
          sender: 'assistant',
          text: 'Hello! I\'d be happy to help you with your account. What specific issue are you experiencing?',
          timestamp: '10:31 AM'
        }
      ]
    }
  } finally {
    chatLoading.value = false
  }
}

// Show delete confirmation modal
const showDeleteConfirmation = (conversationId) => {
  conversationToDelete.value = conversationId
  showDeleteModal.value = true
}

// Cancel delete
const cancelDelete = () => {
  showDeleteModal.value = false
  conversationToDelete.value = null
}

// Confirm delete
const confirmDelete = () => {
  showDeleteModal.value = false
  deleteConversation(conversationToDelete.value)
}

const deleteConversation = async (conversationId) => {
  deleteLoading.value = true
  
  try {
    console.log('Deleting conversation:', conversationId)
    await apiClient.delete(`/api/v1/conversations/${conversationId}`)
    
    // Remove conversation from list
    const index = conversations.value.findIndex(conv => conv === conversationId)
    if (index > -1) {
      conversations.value.splice(index, 1)
      // If the deleted conversation was selected, clear the selection
      if (selectedConversation.value?.id === conversationId) {
        selectedConversation.value = null
      }
    }
    
    // Show success message
    showNotificationMessage('success', 'Success!', `Conversation ${conversationId} has been deleted successfully.`)
    
  } catch (err) {
    console.error('Error deleting conversation:', err)
    
    // Show user-friendly error message
    if (err.message.includes('CORS') || err.message.includes('Network Error')) {
      showNotificationMessage('error', 'CORS Error', 'Cannot delete conversation due to server configuration.')
    } else if (err.response?.status === 404) {
      showNotificationMessage('error', 'Not Found', 'Conversation not found on server.')
    } else if (err.response?.status >= 500) {
      showNotificationMessage('error', 'Server Error', 'Server error while deleting conversation.')
    } else {
      showNotificationMessage('error', 'Delete Failed', 'Failed to delete conversation from server.')
    }
    
    // Fallback to local delete if API fails
    const index = conversations.value.findIndex(conv => conv === conversationId)
    if (index > -1) {
      conversations.value.splice(index, 1)
      if (selectedConversation.value?.id === conversationId) {
        selectedConversation.value = null
      }
      // Show fallback success message
      showNotificationMessage('warning', 'Partially Successful', `Conversation ${conversationId} removed from local list (API error occurred).`)
    }
  } finally {
    deleteLoading.value = false
    conversationToDelete.value = null
  }
}

// Send message function
const sendMessage = async () => {
  if (newMessage.value.trim() && selectedConversation.value) {
    const messageText = newMessage.value.trim()
    
    // Add user message to UI immediately
    const userMessage = {
      id: selectedConversation.value.messages.length + 1,
      sender: 'user',
      text: messageText,
      timestamp: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
    }
    
    selectedConversation.value.messages.push(userMessage)
    newMessage.value = ''
    
    try {
      console.log('Sending message to conversation:', selectedConversation.value.id)
      
      const response = await apiClient.post(`/api/v1/conversations/${selectedConversation.value.id}/messages`, {
        content: messageText,
        role: 'human'
      })
      
      console.log('Message sent successfully:', response.data)
      
      // If server returns updated conversation, refresh the chat
      if (response.data && response.data.messages) {
        const formattedMessages = response.data.messages.map((message, index) => ({
          id: index + 1,
          sender: message.role === 'human' ? 'user' : 'assistant',
          text: message.content,
          timestamp: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
        }))
        
        selectedConversation.value.messages = formattedMessages
      }
      
    } catch (err) {
      console.error('Error sending message:', err)
      
      if (err.response?.status === 404) {
        error.value = 'Conversation not found'
      } else if (err.response?.status >= 500) {
        error.value = 'Server error while sending message'
      } else {
        error.value = 'Failed to send message'
      }
      
      // Message already added to UI, so keep it there even if API fails
    }
  }
}

// Refresh conversations
const refreshConversations = () => {
  fetchConversations()
}

// Fetch conversations when component mounts
onMounted(() => {
  fetchConversations()
})
</script>

<style scoped>
/* Custom styles if needed */
</style>

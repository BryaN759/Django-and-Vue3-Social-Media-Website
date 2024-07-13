<template>
    <div class="max-w-7xl mx-auto grid grid-cols-4 gap-4">
        <div class="main-left col-span-1">
            <div class="p-4 bg-gray-600 border border-gray-800 rounded-lg">
    <div class="flex flex-col">
        <div 
            class="flex items-center justify-between bg-gray-700 p-2 mb-2 rounded-lg cursor-pointer"
            v-for="conversation in conversations"
            v-bind:key="conversation.id"
            v-on:click="setActiveConversation(conversation.id)"
        >
            <div class="flex items-center space-x-2">
                <template
                    v-for="user in conversation.users"
                    v-bind:key="user.id"
                >
                    <img :src="user.get_avatar" class="w-[40px] rounded-full">
                    <p 
                        class="text-xs font-bold text-white"
                        v-if="user.id !== userStore.user.id"
                    >{{ user.name }}</p>
                </template>
            </div>
            <span class="text-xs text-gray-400">{{ conversation.modified_at_formatted }} ago</span>
        </div>
    </div>
</div>
        </div>

        <div class="main-center col-span-3 space-y-4">
            <div class="bg-gray-600 border border-gray-800 rounded-lg">
                <div class="flex flex-col flex-grow p-4">
                    <template
                        v-for="message in activeConversation.messages"
                        v-bind:key="message.id"
                    >
                        <div 
                            class="flex w-full mt-2 space-x-3 max-w-md ml-auto justify-end"
                            v-if="message.created_by.id == userStore.user.id"
                        >
                            <div>
                                <div class="bg-blue-600 text-white p-3 rounded-l-lg rounded-br-lg">
                                    <p class="text-sm">{{ message.body }}</p>
                                </div>
                                <span class="text-xs text-gray-500 leading-none">{{ message.created_at_formatted }} ago</span>
                            </div>
                            <div class="flex-shrink-0 h-10 w-10 rounded-full bg-gray-300">
                                <img :src="message.created_by.get_avatar" class="w-[40px] rounded-full">
                            </div>
                        </div>

                        <div 
                            class="flex w-full mt-2 space-x-3 max-w-md"
                            v-else
                        >
                            <div class="flex-shrink-0 h-10 w-10 rounded-full bg-gray-300">
                                <img :src="message.created_by.get_avatar" class="w-[40px] rounded-full">
                            </div>
                            <div>
                                <div class="bg-gray-500 p-3 rounded-r-lg rounded-bl-lg">
                                    <p class="text-sm text-white">{{ message.body }}</p>
                                </div>
                                <span class="text-xs text-gray-500 leading-none">{{ message.created_at_formatted }} ago</span>
                            </div>
                        </div>
                    </template>
                </div>
            </div>

            <div class="bg-gray-600 border border-gray-800 rounded-lg">
                <form v-on:submit.prevent="submitForm" class="text-gray-200">
                    <div class="p-4 flex items-center">
                        <textarea v-model="body" class="p-4 w-full bg-gray-600 rounded-lg h-14 placeholder-gray-400" placeholder="Type your message here......"></textarea>
                        <button class="ml-4 py-4 px-6 bg-black text-white rounded-lg flex items-center gap-2 h-14">
                            <span>Send</span>
                            <svg viewBox="0 0 28 28" xmlns="http://www.w3.org/2000/svg" fill="currentColor" class="w-6 h-6">
                                <path d="M3.78963301,2.77233335 L24.8609339,12.8499121 C25.4837277,13.1477699 25.7471402,13.8941055 25.4492823,14.5168992 C25.326107,14.7744476 25.1184823,14.9820723 24.8609339,15.1052476 L3.78963301,25.1828263 C3.16683929,25.4806842 2.42050372,25.2172716 2.12264586,24.5944779 C1.99321184,24.3238431 1.96542524,24.015685 2.04435886,23.7262618 L4.15190935,15.9983421 C4.204709,15.8047375 4.36814355,15.6614577 4.56699265,15.634447 L14.7775879,14.2474874 C14.8655834,14.2349166 14.938494,14.177091 14.9721837,14.0981464 L14.9897199,14.0353553 C15.0064567,13.9181981 14.9390703,13.8084248 14.8334007,13.7671556 L14.7775879,13.7525126 L4.57894108,12.3655968 C4.38011873,12.3385589 4.21671819,12.1952832 4.16392965,12.0016992 L2.04435886,4.22889788 C1.8627142,3.56286745 2.25538645,2.87569101 2.92141688,2.69404635 C3.21084015,2.61511273 3.51899823,2.64289932 3.78963301,2.77233335 Z"></path>
                            </svg>
                        </button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</template>
<script>
import axios from 'axios'
import { useUserStore } from '@/stores/user'

export default {
    name: 'chat',

    setup() {
        const userStore = useUserStore()

        return {
            userStore
        }
    },

    data() {
        return {
            conversations: [],
            activeConversation: {},
            body: ''
        }
    },

    mounted() {
        this.getConversations()
    },
    
    methods: {
        setActiveConversation(id) {
            console.log('setActiveConversation', id)

            this.activeConversation = id
            this.getMessages()
        },
        getConversations() {
            console.log('getConversations')

            axios
                .get('/api/chat/')
                .then(response => {
                    console.log(response.data)

                    this.conversations = response.data

                    if (this.conversations.length) {
                        this.activeConversation = this.conversations[0].id
                    }

                    this.getMessages()
                })
                .catch(error => {
                    console.log(error)
                })
        },

        getMessages() {
            console.log('getMessages')

            axios
                .get(`/api/chat/${this.activeConversation}/`)
                .then(response => {
                    console.log(response.data)

                    this.activeConversation = response.data
                })
                .catch(error => {
                    console.log(error)
                })
        },

        submitForm() {
            if (!this.body) {
                return
            }
            console.log('submitForm', this.body)

            axios
                .post(`/api/chat/${this.activeConversation.id}/send/`, {
                    body: this.body
                })
                .then(response => {
                    console.log(response.data)

                    this.activeConversation.messages.push(response.data)
                    this.body = ''
                })
                .catch(error => {
                    console.log(error)
                })
        }
    }
}
</script>
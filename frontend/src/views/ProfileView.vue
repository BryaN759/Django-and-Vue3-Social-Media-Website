<template>
    <main class="px-8 py-6 bg-gray-800">
            <div class="max-w-7xl mx-auto grid grid-cols-4 gap-4">
                <div class="main-left col-span-1">
                    <div class="p-4 bg-gray-600 border border-gray-800 text-center rounded-lg">
                        <img src="https://www.w3schools.com/howto/img_avatar.png" class="mb-6 mt-8 rounded-full w-48 h-48 mx-auto">
                        
                        <p class="text-gray-200"><strong>{{user.name}}</strong></p>

                        <div class="mt-6 flex space-x-8 justify-around">
                            <RouterLink :to="{name: 'friends', params: {id: user.id}}" class="text-xs text-gray-200">{{ user.friends_count }} friends</RouterLink>
                            <p class="text-xs text-gray-200">120 posts</p>
                        </div>

                        <div class="mt-6 mb-4">
                            <button class="inline-block py-4 px-3 bg-black text-xs text-gray-200 rounded-lg"
                                @click="sendFriendRequest"
                                v-if="userStore.user.id !== user.id"
                            >
                                Send request
                            </button>
                            <button 
                                class="inline-block ml-4 py-4 px-3 bg-black text-xs text-gray-200 rounded-lg" 
                                @click="sendDirectMessage"
                                v-if="userStore.user.id !== user.id"
                            >
                                Message
                            </button>

                            <button class="inline-block py-4 px-6 bg-black border border-red-500 text-red-500 rounded-lg"
                            @click="logout"
                            v-if="userStore.user.id === user.id"
                            >Log out</button>
                        </div>
                    </div>
                </div>

                <div class="main-center col-span-2 space-y-4">
                    <div 
                    class="bg-gray-600 border border-gray-800 rounded-lg"
                        v-if="userStore.user.id === user.id"
                    >
                        <form v-on:submit.prevent="submitForm" method="post">
                        <div class="p-4">  
                            <textarea v-model="body" class="p-4 w-full bg-gray-500 rounded-lg" placeholder="What are you thinking about?"></textarea>
                        </div>

                        <div class="p-4 border-t border-gray-500 flex justify-between">
                            <div class="flex space-x-4 mt-3">
                                <label class="inline-block">
                                    <img src="https://cdn-icons-png.flaticon.com/512/4211/4211763.png" class="w-8 h-8 cursor-pointer" />
                                    <input type="file" ref="file" @change="onFileChange" class="hidden">
                                </label>
                                <img src="https://cdn-icons-png.flaticon.com/128/739/739272.png" class="w-8 h-8 cursor-pointer" />
                                <img src="https://cdn-icons-png.flaticon.com/128/158/158420.png" class="w-8 h-8 cursor-pointer" />
                                <img src="https://cdn-icons-png.flaticon.com/128/7349/7349652.png" class="w-8 h-8 cursor-pointer" />
                            </div>

                            <button class="inline-block py-4 px-6 bg-black text-gray-200 rounded-3xl">Post</button>
                        </div>
                    </form>
                    </div>

                    <div 
                        class="p-4 bg-gray-600 border border-gray-800 rounded-lg"
                        v-for="post in posts"
                        v-bind:key="post.id"
                    >
                        <FeedItem v-bind:post="post" />
                    </div>
                </div>

                <div class="main-right col-span-1 space-y-4">
                    <PeopleYouMayKnow />
                    <Trends />
                </div>
            </div>
        </main>
</template>

<script>

import axios from 'axios';
import PeopleYouMayKnow from '../components/PeopleYouMayKnow.vue'
import Trends from '../components/Trends.vue'
import FeedItem from '@/components/FeedItem.vue'
import { useUserStore } from '@/stores/user'
import { useToastStore } from '@/stores/toast'

export default {
    name: 'FeedView',

    setup() {
        const userStore = useUserStore()
        const toastStore = useToastStore()

        return {
            userStore,
            toastStore
        }
    },

    components: {
        PeopleYouMayKnow,
        Trends,
        FeedItem,
    },

    data() {
        return {
            posts: [],
            user: {
                id: null
            },
            body: '',
        }
    },

    mounted() {
        this.getFeed()   
    },

    watch: { 
        '$route.params.id': {
            handler: function() {
                this.getFeed()
            },
            deep: true,
            immediate: true
        }
    },

    methods: {
        sendDirectMessage() {
            console.log('sendDirectMessage')

            axios
                .get(`/api/chat/${this.$route.params.id}/get-or-create/`)
                .then(response => {
                    console.log(response.data)

                    this.$router.push('/chat')
                })
                .catch(error => {
                    console.log('error', error)
                })
        },

        sendFriendRequest() {
            axios
                .post(`/api/friends/${this.$route.params.id}/request/`)
                .then(response => {
                    console.log('data', response.data)


                    if (response.data.message == 'request already sent'){
                        this.toastStore.showToast(5000, 'The request is already been sent', 'bg-red-300')
                    } else {
                        this.toastStore.showToast(5000, 'The request was sent', 'bg-emerald-300')

                    }
                })
                .catch(error => {
                    console.log('error', error)
                })
        },

        getFeed() {
            axios
                .get(`/api/posts/profile/${this.$route.params.id}/`)
                .then(response => {
                    console.log('data', response.data)

                    this.posts = response.data.posts
                    this.user = response.data.user
                })
                .catch(error => {
                    console.log('error', error)
                })
        },

        submitForm() {
            console.log('submitform', this.body)

            axios
                .post('/api/posts/create/', {
                    'body': this.body
                })
                .then(response => {
                    console.log('data', response.data)

                    this.posts.unshift(response.data)
                    this.body = ''
                })
                .catch(error => {
                    console.log('error', error)
                })
        },
        logout() {
            console.log('Log out')

            this.userStore.removeToken()

            this.$router.push('/login')
        }
    }
}
</script>
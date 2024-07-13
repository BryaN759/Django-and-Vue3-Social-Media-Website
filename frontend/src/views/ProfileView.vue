<template>
    <main class="px-8 py-6 bg-gray-800">
            <div class="max-w-7xl mx-auto grid grid-cols-4 gap-4">
                <div class="main-left col-span-1">
                    <div class="p-4 bg-gray-600 border border-gray-800 text-center rounded-lg">
                        <img :src="user.get_avatar" class="mb-6 mt-8 rounded-full w-48 h-48 mx-auto">
                        
                        <p class="text-gray-200"><strong>{{user.name}}</strong></p>

                        <div class="mt-6 flex space-x-8 justify-around">
                            <RouterLink :to="{name: 'friends', params: {id: user.id}}" class="text-xs text-gray-200">{{ user.friends_count }} friends</RouterLink>
                            <p class="text-xs text-gray-200">{{ user.posts_count }} posts</p>
                        </div>

                        <div class="mt-6 mb-4 flex items-center justify-center">
                            <button class="inline-block mt-4 py-4 px-6"
                                @click="sendFriendRequest"
                                v-if="userStore.user.id !== user.id"
                            >
                                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 45.902 45.902" class="w-5 h-5 fill-current text-gray-200">
                                    <path d="M43.162 26.681c-1.564-1.578-3.631-2.539-5.825-2.742 1.894-1.704 3.089-4.164 3.089-6.912 0-5.141-4.166-9.307-9.308-9.307-4.911 0-8.932 3.804-9.281 8.625 4.369 1.89 7.435 6.244 7.435 11.299 0 1.846-0.42 3.65-1.201 5.287 1.125 0.588 2.162 1.348 3.066 2.26 2.318 2.334 3.635 5.561 3.61 8.851l-0.002 0.067-0.002 0.057-0.082 1.557h11.149l0.092-12.33c-0.021-2.231-1.006-4.643-2.78-6.428zM23.184 34.558c1.893-1.703 3.092-4.164 3.092-6.912 0-5.142-4.168-9.309-9.309-9.309s-9.309 4.167-9.309 9.309c0 2.743 1.194 5.202 3.084 6.906-4.84 0.375-8.663 4.383-8.698 9.318l-0.092 1.853h14.153h15.553l0.092-1.714c0.018-2.514-0.968-4.926-2.741-6.711-2.075-2.036-4.141-2.994-6.334-3.197zM6.004 11.374v3.458c0 1.432 1.164 2.595 2.597 2.595s2.597-1.163 2.597-2.595v-3.458h3.454c1.433 0 2.596-1.164 2.596-2.597s-1.163-2.596-2.596-2.596h-3.454V2.774c0-1.433-1.162-2.595-2.597-2.595s-2.597 1.162-2.597 2.595V6.18H2.596C1.161 6.18 0 7.344 0 8.776s1.161 2.597 2.596 2.597H6.004z"/>
                                </svg>
                            </button>

                            <button 
                                class="inline-block mt-4 py-4 px-6" 
                                @click="sendDirectMessage"
                                v-if="userStore.user.id !== user.id"
                            >
                                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 32 32" class="w-6 h-6 fill-current text-gray-200">
                                    <path fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" stroke-miterlimit="10" d="M26.4 2.9L3.8 15c-0.7 0.4-0.7 1.5 0.1 1.8l20.8 8.7c0.6 0.3 1.3-0.2 1.4-0.8l1.7-20.8c0.1-0.7-0.7-1.2-1.4-0.8z"/>
                                    <path fill="none" stroke="currentColor" stroke-width="2" stroke-linejoin="round" stroke-miterlimit="10" d="M26 4L13 20v7.3c0 0.9 1.2 1.4 1.8 0.7L19 23"/>
                                </svg>
                            </button>


                            <RouterLink 
                                class="inline-block mt-4 py-4 px-6" 
                                to="/profile/edit"
                                v-if="userStore.user.id === user.id"
                            >
                                <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                                    <path d="M2,21H8a1,1,0,0,0,0-2H3.071A7.011,7.011,0,0,1,10,13a5.044,5.044,0,1,0-3.377-1.337A9.01,9.01,0,0,0,1,20,1,1,0,0,0,2,21ZM10,5A3,3,0,1,1,7,8,3,3,0,0,1,10,5ZM20.207,9.293a1,1,0,0,0-1.414,0l-6.25,6.25a1.011,1.011,0,0,0-.241.391l-1.25,3.75A1,1,0,0,0,12,21a1.014,1.014,0,0,0,.316-.051l3.75-1.25a1,1,0,0,0,.391-.242l6.25-6.25a1,1,0,0,0,0-1.414Zm-5,8.583-1.629.543.543-1.629L19.5,11.414,20.586,12.5Z" fill="#FFFFFF"/>
                                </svg>
                            </RouterLink>

                            <button class="inline-block mt-4 py-4 px-6"
                                @click="logout"
                                v-if="userStore.user.id === user.id"
                            >
                                
                                <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                                    <path d="M12 3V12M18.3611 5.64001C19.6195 6.8988 20.4764 8.50246 20.8234 10.2482C21.1704 11.994 20.992 13.8034 20.3107 15.4478C19.6295 17.0921 18.4759 18.4976 16.9959 19.4864C15.5159 20.4752 13.776 21.0029 11.9961 21.0029C10.2162 21.0029 8.47625 20.4752 6.99627 19.4864C5.51629 18.4976 4.36274 17.0921 3.68146 15.4478C3.00019 13.8034 2.82179 11.994 3.16882 10.2482C3.51584 8.50246 4.37272 6.8988 5.6311 5.64001" stroke="#FF7F7F" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                                </svg>
                            </button>
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

                    this.posts.unshift(response.data.posts)
                    this.user = response.data.user
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
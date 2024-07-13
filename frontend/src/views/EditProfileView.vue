
<template>
    <div class="max-w-7xl mx-auto grid grid-cols-2 gap-4">
      <div class="main-left">
        <div class="p-12 bg-gray-600 border border-gray-800 rounded-lg">
          <h1 class="mb-6 text-2xl text-gray-200">Edit profile</h1>
          <p class="mb-6 text-gray-400">
            Lorem ipsum dolor sit mate. Lorem ipsum dolor sit mate. Lorem ipsum dolor sit mate.
            Lorem ipsum dolor sit mate. Lorem ipsum dolor sit mate. Lorem ipsum dolor sit mate.
          </p>
          <RouterLink to="/profile/edit/password" class="underline text-gray-200">Edit password</RouterLink>
        </div>
      </div>
  
      <div class="main-right">
        <div class="p-12 bg-gray-600 border border-gray-800 text-gray-200 rounded-lg">
          <form class="space-y-6" v-on:submit.prevent="submitForm">
            <div>
              <label>Name</label><br>
              <input type="text" v-model="form.name" placeholder="Your full name" class="w-full mt-2 py-4 px-6 bg-gray-500 border border-gray-600 rounded-lg">
            </div>
  
            <div>
              <label>E-mail</label><br>
              <input type="email" v-model="form.email" placeholder="Your e-mail address" class="w-full mt-2 py-4 px-6 bg-gray-500 border border-gray-600 rounded-lg">
            </div>
  
            <div class="flex items-center mt-2">
            <label class="inline-block mt-4 py-6 px-10 bg-gray-600 text-gray-500 border border-gray-500 text-sm rounded-lg text-center cursor-pointer">
                <span class="block mb-2">Add Avatar</span>
                <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg" class="w-10 h-10 mx-auto">
                <circle cx="12" cy="12" r="10" stroke="#737373" stroke-width="1.5"/>
                <path d="M15 12L12 12M12 12L9 12M12 12L12 9M12 12L12 15" stroke="#737373" stroke-width="1.5" stroke-linecap="round"/>
                </svg>
                <input type="file" ref="file" class="hidden" @change="handleFileChange">
            </label>
                <div v-if="fileName" class="ml-4 mt-4 text-gray-200">
                    {{ fileName }}
                </div>
            </div>
  
            <template v-if="errors.length > 0">
              <div class="bg-red-300 text-white rounded-lg p-6">
                <p v-for="error in errors" v-bind:key="error">{{ error }}</p>
              </div>
            </template>
  
            <div>
              <button class="py-4 px-6 bg-gray-900 text-gray-200 text-sm rounded-lg">Save changes</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </template>
  
  
  

  <script>
import axios from 'axios'

import { useToastStore } from '@/stores/toast'
import { useUserStore } from '@/stores/user'

export default {
    setup() {
        const toastStore = useToastStore()
        const userStore = useUserStore()

        return {
            toastStore,
            userStore
        }
    },

    data() {
        return {
            form: {
                email: this.userStore.user.email,
                name: this.userStore.user.name
            },
            errors: [],
            fileName: '', // To store the file name
        }
    },

    mounted() {
        // Initialize the fileName with the current file name from the database
        this.fileName = this.userStore.user.avatar ? this.userStore.user.avatar.split('/').pop() : '';
    },

    methods: {
        submitForm() {
            this.errors = []

            if (this.form.email === '') {
                this.errors.push('Your e-mail is missing')
            }

            if (this.form.name === '') {
                this.errors.push('Your name is missing')
            }

            if (this.errors.length === 0) {
                let formData = new FormData()
                if (this.$refs.file.files[0]) {
                    formData.append('avatar', this.$refs.file.files[0])
                }
                formData.append('name', this.form.name)
                formData.append('email', this.form.email)

                axios
                    .post('/api/editprofile/', formData, {
                        headers: {
                            "Content-Type": "multipart/form-data",
                        }
                    })
                    .then(response => {
                        if (response.data.message === 'information updated') {
                            this.toastStore.showToast(5000, 'The information was saved', 'bg-emerald-500')

                            this.userStore.setUserInfo({
                                id: this.userStore.user.id,
                                name: this.form.name,
                                email: this.form.email,
                                avatar: response.data.user.get_avatar
                            })

                            this.$router.back()
                        } else {
                            this.toastStore.showToast(5000, `${response.data.message}. Please try again`, 'bg-red-300')
                        }
                    })
                    .catch(error => {
                        console.log('error', error)
                    })
            }
        },

        fileChanged(event) {
            const file = event.target.files[0];
            if (file) {
                this.fileName = file.name;
            }
        }
    }
}
</script>


  

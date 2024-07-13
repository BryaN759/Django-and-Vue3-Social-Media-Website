<template>
    <div class="p-4 bg-gray-600 border border-gray-800 rounded-lg">
        <h3 class="mb-6 text-xl text-white">Trends</h3>

        <div class="space-y-4">
            <div 
                class="flex items-center justify-between"
                v-for="trend in trends"
                v-bind:key="trend.id"
            >
                    <p class="text-xs text-gray-200">
                        <strong>#{{ trend.hashtag }}</strong><br>
                        <span class="text-gray-400">{{ trend.occurences }} posts</span>
                    </p>

                <RouterLink :to="{name: 'trendview', params: {id: trend.hashtag}}"  class="py-2 px-3 bg-black text-gray-200 text-xs rounded-lg">Explore</RouterLink>
            </div>                           
        </div>
    </div>
</template>


<script>
import axios from 'axios'

export default {
    name: 'trends',

    data() {
        return {
            trends: []
        }
    },

    mounted() {
        this.getTrends()
    },

    methods: {
        getTrends() {
            axios
                .get('/api/posts/trends/')
                .then(response => {
                    console.log(response.data)

                    this.trends = response.data
                })
                .catch(error => {
                    console.log('Error: ', error)
                })
        }
    }
}
</script>
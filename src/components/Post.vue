<template>
  <form class="form-widget" @submit.prevent="addTweet">
    <div>
      <label for="tweet">Tweet</label>
      <input id="tweet" type="text" v-model="tweet" />
    </div>

    <div>
      <input
        type="submit"
        class="button block primary"
        :value="loading ? 'Loading ...' : 'Tweet'"
        :disabled="loading"
      />
    </div>
  </form>
</template>

<script>
import { supabase } from "../supabase"
import { store } from "../store"
import { onMounted, ref } from "vue"

export default {
  setup() {
    const loading = ref(false)
    const tweet = ref("")

    async function addTweet() {
      try {
        loading.value = true
        store.user = supabase.auth.user()

        // tweet object
        const updates = {
          user_id: store.user.id,
          text: tweet.value,
          updated_at: new Date(),
        }

        let { error } = await supabase.from("tweets").upsert(updates, {
          returning: "minimal", // Don't return the value after inserting
        })

        if (error) throw error
      } catch (error) {
        alert(error.message)
      } finally {
        loading.value = false
        tweet.value = ''
      }
    }

    return {
      store,
      loading,
      tweet,

      addTweet,
    }
  },
}
</script>

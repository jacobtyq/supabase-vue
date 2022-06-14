<template>
  <div class="container" style="padding: 50px 0 100px 0">
    <h2>Tweets</h2>
    <Post v-if="store.user" />
    <Posts />
  </div>
  <div class="container" style="padding: 50px 0 100px 0">
    <Profile v-if="store.user" />
    <Auth v-else />
  </div>
</template>

<script>
import { store } from "./store"
import { supabase } from "./supabase"
import Auth from "./components/Auth.vue"
import Profile from "./components/Profile.vue"
import Post from "./components/Post.vue"
import Posts from "./components/Posts.vue"
export default {
  components: {
    Auth,
    Profile,
    Post,
    Posts
  },

  setup() {
    store.user = supabase.auth.user()
    supabase.auth.onAuthStateChange((_, session) => {
      store.user = session.user
    })

    return {
      store,
    }
  },
}
</script>

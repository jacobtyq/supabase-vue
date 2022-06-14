<template>
  <ul>
    <li v-for="(tweet, key) in tweets">{{ tweet.text }}</li>
  </ul>
</template>

<script>
import { supabase } from "../supabase";
import { store } from "../store";
import { onMounted, ref } from "vue";

export default {
  setup() {
    const loading = ref(false);
    let tweets = ref([])

    async function getTweets() {
      try {
        loading.value = true;

        // FIXME: create a policy for read-only of tweets
        let { data, error, status } = await supabase
          .from("tweets")
          .select(`user_id, text, updated_at`)
          .order("updated_at", { ascending: false });

        if (error && status !== 406) throw error;

        if (data) {
          console.log("tweets", data);
          tweets.value = data.filter(tweet => tweet.text.length);
        }
      } catch (error) {
        alert(error.message);
      } finally {
        loading.value = false;
      }
    }

    onMounted(() => {
      getTweets();
    });

    return {
      store,
      loading,
      tweets,

      getTweets,
    };
  },
};
</script>

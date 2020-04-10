<template>
  <v-app>
    <v-content>
      <v-row>
        <v-col :cols="calcCols" v-for="(item, index) of websites" :key="index">
          <source-page class="d-inline" :website-url="item" />
        </v-col>
      </v-row>
    </v-content>
  </v-app>
</template>

<script>
import SourcePage from "./components/SourcePage";

export default {
  name: "App",

  components: {
    SourcePage
  },

  data: () => ({
    websites: [
      "heise.de/rss/heise-atom.xml",
      "https://rss.golem.de/rss.php?feed=ATOM1.0",
    ]
  }),

  computed: {
    calcCols () {
      if (this.websites.length === 0) return 12

      return 12 / this.websites.length
    }
  }
};
</script>

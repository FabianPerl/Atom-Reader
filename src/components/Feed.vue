<template>
  <div>
    <v-list-item @click="openLink(link)">
      <v-list-item-content>
        <v-list-item-title>{{ formatDate }} {{ title }}</v-list-item-title>
        <v-list-item-subtitle>{{ summary }}</v-list-item-subtitle>
      </v-list-item-content>
    </v-list-item>
    <v-divider />
  </div>
</template>

<script>
export default {
  data: () => ({
    color: "black"
  }),
  props: {
    title: {
      type: String,
      default: ""
    },
    summary: {
      type: String,
      default: ""
    },
    link: {
      type: Object
    },
    published: {
        type: String
    }
  },
  methods: {
    openLink(link) {
      window.open(link["@attributes"].href, "_blank");
    }
  },
  computed: {
    formatDate() {
      let date = new Date(this.published);
      return date.toLocaleDateString() + " " + date.toLocaleTimeString();
    }
  }
};
</script>

<style>
</style>
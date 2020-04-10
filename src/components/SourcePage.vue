<template>
  <div>
    <h1>{{ page.title }}</h1>
    <h2>{{ page.subtitle }} - {{ formatDate }}</h2>
    <v-btn outlined @click="fetchData">Update</v-btn>
    <div>
      <v-list three-line>
        <v-list-item-group v-model="selected" multiple active-class="blue--text">
          <v-subheader>{{ page.title }}</v-subheader>
          <feed
            v-for="(item, index) of page.feeds"
            :key="index"
            :title="item.title"
            :summary="item.summary"
            :link="item.link"
            :published="item.published"
          />
        </v-list-item-group>
      </v-list>
    </div>
  </div>
</template>

<script>
import Feed from "./Feed";

export default {
  components: {
    Feed
  },
  data: () => ({
    selected: [],
    page: {
      feeds: [],
      title: null,
      subtitle: null,
      updated: null
    }
  }),
  props: {
    websiteUrl: {
      type: String,
      default: null
    }
  },
  methods: {
    xmlToJson(xml) {
      var obj = {};

      if (xml.nodeType == 1) {
        if (xml.attributes.length > 0) {
          obj["@attributes"] = {};
          for (var j = 0; j < xml.attributes.length; j++) {
            var attribute = xml.attributes.item(j);
            obj["@attributes"][attribute.nodeName] = attribute.nodeValue;
          }
        }
      } else if (xml.nodeType == 3) {
        obj = xml.nodeValue;
      }

      var textNodes = [].slice.call(xml.childNodes).filter(function(node) {
        return node.nodeType === 3;
      });
      if (xml.hasChildNodes() && xml.childNodes.length === textNodes.length) {
        obj = [].slice.call(xml.childNodes).reduce(function(text, node) {
          return text + node.nodeValue;
        }, "");
      } else if (xml.hasChildNodes()) {
        for (var i = 0; i < xml.childNodes.length; i++) {
          var item = xml.childNodes.item(i);
          var nodeName = item.nodeName;
          if (typeof obj[nodeName] == "undefined") {
            obj[nodeName] = this.xmlToJson(item);
          } else {
            if (typeof obj[nodeName].push == "undefined") {
              var old = obj[nodeName];
              obj[nodeName] = [];
              obj[nodeName].push(old);
            }
            obj[nodeName].push(this.xmlToJson(item));
          }
        }
      }
      return obj;
    },
    fetchData() {
      fetch("https://cors-anywhere.herokuapp.com/" + this.websiteUrl)
        .then(succ => {
          succ
            .text()
            .then(xml => {
              let parser = new DOMParser();
              let xmlDoc = parser.parseFromString(xml, "text/xml");
              let json = this.xmlToJson(xmlDoc);

              // eslint-disable-next-line no-console
              console.log(json)

              this.page.title = json.feed.title;
              this.page.subtitle = json.feed.subtitle;
              this.page.updated = json.feed.updated;
              this.page.feeds = json.feed.entry
            //   .sort((a, b)=> a.published - b.published);
            })
            .catch(err => {
              // eslint-disable-next-line no-console
              console.log(err);
            });
        })
        .catch(err => {
          // eslint-disable-next-line no-console
          console.log(err);
        });
    }
  },
  computed: {
    formatDate() {
      let date = new Date(this.page.updated);
      return date.toLocaleDateString() + " " + date.toLocaleTimeString();
    }
  },
  mounted() {
    this.fetchData();
    setInterval(this.fetchData(), 300000); // every 5 minutes fetch new data
  }
};
</script>

<style>
</style>
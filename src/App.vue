<template>
  <v-app dark>
    <v-main>
      <template v-if="!loading">
          <v-btn @mouseup="beginAuth">Click Here to Authorize with Discord</v-btn>
      </template>
      <template v-else>
          <p>Tab out and check your browser to finish authentication. <a @mousedown="authAgain">Re-open Window.</a></p>
      </template>
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: 'App',
  data() {
      return {
          url: null,
          loading: false,
          done: false,
          updates: 0,
      };
  },
  methods: {
      setAsReady() {
        this.$nextTick(() => {
            this.updates += 1;
        });
      },
      beginAuth() {
          setTimeout(() => {
              this.getURL();
              this.loading = true;
              this.updates += 1;
          }, 100);
      },
      authAgain() {
          this.getURL();
      },
      getURL() {
          if ('alt' in window) {
              alt.emit('discord:OpenURL');
          }
      },
      openURL(url) {
          window.open(url);
      },
      loadAthena() {
          window.open(`https://github.com/stuyk/altv-athena`);
      },
      finishedLoading() {
          this.$nextTick(() => {
              this.setAsReady();
          });
      },
      fadeToBlack() {
          this.done = true;
      }
  },
  mounted() {
    if ('alt' in window) {
        alt.on('discord:OpenURL', this.openURL);
        alt.on('discord:FadeToBlack', this.fadeToBlack);
    }

    this.finishedLoading();
  }
};
</script>

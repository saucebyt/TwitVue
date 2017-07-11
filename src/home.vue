<template lang="jade">
  div#app
    f7-views(navbar-through='')
      f7-view(main='', url='/', :dynamic-navbar='true')
        f7-navbar
          f7-nav-left
          f7-nav-center(sliding='') TwitVue
          f7-nav-right
        f7-pages#pages
          f7-page.navbar-fixed
            f7-searchbar(cancel-link='Cancel', placeholder='Search in items', :clear-button='true', v-on:change="onChange")
            f7-list.tweets(media-list='')
              f7-list-item(v-on:click='onClick(tweet)', link='/tweet/', v-for='tweet in tweets', :media='tweet.user.profile_image_html', :title='tweet.user.name', :subtitle='tweet.user.screen_name_at', :text='tweet.text')
</template>

<script>
  import cb from './twitter.js';

  let self;

  function addData(tweets) {
    for (let tweet of tweets) {
      tweet.user.screen_name_at = '@' + tweet.user.screen_name;
      tweet.user.profile_image_html = '<img src="' + tweet.user.profile_image_url + '">';
    }
  }

  export default {
    name: 'app',
    data () {
      return {
        tweets : []
      }
    },
    created () {
      self = this;
    },
    methods : {
      onChange: function (event) {
        let term = event.target.value;
        if (!term) {
          self.tweets = [];
        }
        else {
          window.f7.showPreloader();
          cb.__call(
              "search_tweets",
              "q=" + term,
              function (reply) {
                console.log(reply.statuses);
                self.tweets = reply.statuses;
                addData(self.tweets);
                window.f7.hidePreloader();
              },
              true // this param is required
          );
        }
      },
      onClick: function (tweet) {
        console.log(tweet);
      }
    }
  }
</script>

<style lang="sass?indentedSyntax">
  /* Add your styles here */
  .tweets
    .item-media
      img
        border-radius: 100%
</style>

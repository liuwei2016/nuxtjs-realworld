<template>
  <div class="article-meta">
    <nuxt-link
      :to="{
      name: 'profile',
      params: {
        username: article.author.username
      }
    }"
    >
      <img :src="article.author.image" />
    </nuxt-link>
    <div class="info">
      <nuxt-link
        class="author"
        :to="{
        name: 'profile',
        params: {
          username: article.author.username
        }
      }"
      >{{ article.author.username }}</nuxt-link>
      <span class="date">{{ article.createdAt | date('MMM DD, YYYY') }}</span>
    </div>
    <button
      @click="onFollow"
      class="btn btn-sm btn-outline-secondary"
      :class="{
        active: following
      }"
    >
      <i class="ion-plus-round"></i>
      &nbsp;
      {{ following ? 'Follow' : "Unfollow " }} {{article.author.username }}
      <span
        class="counter"
      >(10)</span>
    </button>
    &nbsp;&nbsp;
    <button
      class="btn btn-sm btn-outline-primary"
      @click="onFavorite(article)"
      :class="{
        active: favorited
      }"
    >
      <i class="ion-heart"></i>
      &nbsp;
      {{ favorited ? 'Unfavorite' : 'Favorite'}} Post
      <span
        class="counter"
      >({{favoritesCount}})</span>
    </button>
  </div>
</template>

<script>
import {
  addFavorite,
  deleteFavorite,
  unFollow,
  addFollow,
} from "@/api/article";
export default {
  name: "ArticleMeta",
  props: {
    article: {
      type: Object,
      required: true,
    },
  },
  data: function () {
    return {
      followDisabled: false,
      following: false,
      favoritesCount: 0,
      favorited: false,
      favoriteDisabled: null,
    };
  },
  created() {
    this.favoritesCount = this.article.favoritesCount;
    this.favorited = this.article.favorited;
    this.following = this.article.author.following;
  },
  methods: {
    async onFollow(article) {
      if (this.followDisabled) {
        return;
      }
      this.followDisabled = true;
      if (this.following) {
        //
        this.following = false;
        await addFollow(article.slug);
      } else {
        this.following = true;
        await unFollow(article.slug);
      }
      this.followDisabled = false;
    },
    async onFavorite(article) {
      if (this.favoriteDisabled) {
        return;
      }
      this.favoriteDisabled = true;
      if (this.favorited) {
        // 取消点赞
        this.favoritesCount += -1;
        this.favorited = false;

        let res = await deleteFavorite(article.slug);
        // console.log(data);
        this.favoritesCount = res.data.article.favoritesCount;
      } else {
        // 添加点赞
        this.favoritesCount += 1;
        this.favorited = true;
        let res = await addFavorite(article.slug);
        this.favoritesCount = res.data.article.favoritesCount;
        // console.log(data);
      }
      this.favoriteDisabled = false;
    },
  },
};
</script>

<style>
</style>

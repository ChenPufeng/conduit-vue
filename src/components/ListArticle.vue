<template>
  <div>
    <template v-if="loading ? limit : articles.data.length">
      <article-preview
        v-for="(article, index) in articlesData"
        :key="loading ? index :article.slug + index"
        :article="loading ? {} : article"
        :showPlaceholder="loading"
      >
      </article-preview>
    </template>
    <template v-else>
      <div class="article-preview">
        No articles are here... yet.
      </div>
    </template>
    <div class="text-xs-center">
      <the-pagination v-model="offset" :pageCount="pageCount"></the-pagination>
    </div>
  </div>
</template>
<script>
import { mapActions } from 'vuex'
import { FETCH_ARTICLES, FETCH_FEED_ARTICLES } from '@/store/actions.type'
import ThePagination from '@/components/ThePagination'
import ArticlePreview from '@/components/ArticlePreview'
export default {
  props: {
    query: {
      type: Object,
      default: function () {
        return {}
      }
    },
    limit: {
      type: Number,
      default: 10
    },
    feed: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      offset: 0,
      loading: true
    }
  },
  components: {
    ArticlePreview,
    ThePagination
  },
  created () {
    this.fetchNewArticle(this.queryString)
  },
  watch: {
    offset () {
      this.fetchNewArticle(this.queryString)
    },
    query () {
      this.fetchNewArticle(this.queryString)
    }
  },
  computed: {
    articles () {
      return this.$store.state.home.articles
    },
    pageCount () {
      return Math.round(this.articles.articlesCount / this.limit)
    },
    queryString () {
      return Object.assign({}, this.query, { limit: this.limit, offset: this.offset })
    },
    articlesData () {
      if (this.loading) return this.limit
      return this.articles.data
    }
  },
  methods: {
    ...mapActions({
      fetchArticle: FETCH_ARTICLES,
      fetchFeedArticle: FETCH_FEED_ARTICLES
    }),
    async fetchNewArticle () {
      this.loading = true
      if (!this.feed) {
        await this.fetchArticle(this.queryString)
      } else {
        await this.fetchFeedArticle(this.queryString)
      }
      this.loading = false
    }
  }
}
</script>

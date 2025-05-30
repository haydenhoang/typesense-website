<template>
  <!-- Adapted from AlgoliaSearchBox 🙏 -->
  <form id="search-form" class="typesense-search-wrapper search-box" role="search">
    <div class="search-container">
      <input id="typesense-search-input" class="search-query" :placeholder="placeholder" />
      <kbd class="shortcut-indicator"> <span class="shortcut-key">⌘</span>K </kbd>
    </div>
  </form>
</template>

<script>
let debounceTimerId

export default {
  name: 'TypesenseSearchBox',

  props: {
    options: {
      type: Object,
      default: null,
    },
  },

  data() {
    return {
      placeholder: undefined,
    }
  },

  watch: {
    $lang(newValue) {
      this.update(this.options, newValue)
    },

    options(newValue) {
      this.update(newValue, this.$lang)
    },
  },

  mounted() {
    this.initialize(this.options, this.$lang)
    this.placeholder = this.$site.themeConfig.searchPlaceholder || ''
    document.addEventListener('keydown', this.handleKeyDown)
  },

  beforeDestroy() {
    // Clean up the event listener when component is destroyed
    document.removeEventListener('keydown', this.handleKeyDown)
  },

  methods: {
    initialize(userOptions, lang) {
      Promise.all([
        import(/* webpackChunkName: "docsearch" */ '../public/docsearch.min.js'),
        import(/* webpackChunkName: "docsearch" */ '../public/docsearch.min.css'),
      ]).then(([docsearch]) => {
        docsearch = docsearch.default
        const { typesenseSearchParams = {} } = userOptions
        docsearch(
          Object.assign({}, userOptions, {
            inputSelector: '#typesense-search-input',
            // #697 Make docsearch work well at i18n mode.
            typesenseSearchParams: {
              ...typesenseSearchParams,
              filter_by: userOptions.typesenseVersion
                ? `version:=[${userOptions.typesenseVersion},unversioned]`
                : `version:=[${this.$site.themeConfig.typesenseLatestVersion},unversioned]`,
            },
            handleSelected: (input, event, suggestion) => {
              const { pathname, hash } = new URL(suggestion.url)
              const routepath = pathname.replace(this.$site.base, '/')
              const _hash = decodeURIComponent(hash)
              this.$router.push(`${routepath}${_hash}`)
            },
            queryHook: query => {
              if (!window.gtag) {
                return
              }
              if (debounceTimerId) {
                clearTimeout(debounceTimerId)
              }
              debounceTimerId = setTimeout(() => {
                window.gtag('event', 'search', {
                  search_term: query,
                })
              }, 500)
            },
          }),
        )
      })
    },

    update(options, lang) {
      this.$el.innerHTML = `
        <div class="search-container">
          <input id="typesense-search-input" class="search-query">
          <kbd class="shortcut-indicator"><span class="shortcut-key">⌘</span>K</kbd>
        </div>
      `
      this.initialize(options, lang)
    },

    handleKeyDown(event) {
      if ((event.ctrlKey || event.metaKey) && event.key === 'k') {
        event.preventDefault()

        const searchInput = document.getElementById('typesense-search-input')
        if (searchInput) {
          searchInput.focus()
        }
      }
    },
  },
}
</script>

<style lang="stylus">
.typesense-search-wrapper
  margin-right 0

  .search-container
    position relative
    display flex
    align-items center
    width 100%

  .search-query
    padding-right 40px 

  .shortcut-indicator
    position absolute
    right 8px
    top 50%
    transform translateY(-50%)
    display inline-flex
    align-items center
    gap 1px
    height 14px
    border 1px solid rgba(0, 0, 0, 0.1)
    border-radius 7px
    padding 0 4px
    font-size 9px
    font-family monospace
    font-weight 500
    color #666
    background-color #f5f5f5
    pointer-events none
    user-select none

    .shortcut-key
      font-size 11px
      margin-right 1px

  span.typesense-docsearch-suggestion--highlight
    color unset !important
    border-bottom 2px solid $accentColor2 !important
    box-shadow unset !important
    background unset !important

  .typesense-docsearch-suggestion--category-header-lvl0
    span.typesense-docsearch-suggestion--highlight
      border-bottom 1px solid white !important

  & > span
    vertical-align middle
  .algolia-autocomplete
    line-height normal
    .ds-dropdown-menu
      background-color #fff
      border 1px solid #999
      border-radius 4px
      font-size 16px
      margin 6px 0 0
      padding 4px
      text-align left
      &:before
        border-color #999
      [class*=ds-dataset-]
        border none
        padding 0
      .ds-suggestions
        margin-top 0
      .ds-suggestion
        border-bottom 1px solid $borderColor
    .typesense-docsearch-suggestion--highlight
      color #2c815b
    .typesense-docsearch-suggestion
      border-color $borderColor
      padding 0
      .typesense-docsearch-suggestion--category-header
        padding 5px 10px
        margin-top 0
        background $accentColor
        color #fff
        font-weight 600
        .typesense-docsearch-suggestion--highlight
          background rgba(22, 44, 44, 0.6)
      .typesense-docsearch-suggestion--wrapper
        padding 0
      .typesense-docsearch-suggestion--title
        font-weight 600
        margin-bottom 0.8rem
        color $textColor
      .typesense-docsearch-suggestion--subcategory-column
        vertical-align top
        padding 0.8rem
        border-color $borderColor
        background #f1f3f5
        &:after
          display none
      .typesense-docsearch-suggestion--subcategory-column-text
        color #555
    .typesense-docsearch-footer
      border-color $borderColor
    .typesense-docsearch-suggestion--content
      padding 0.8rem
    .ds-cursor .typesense-docsearch-suggestion--content
      background-color #e7edf3 !important
      color $textColor

@media (min-width: $MQMobile)
  .typesense-search-wrapper
    .algolia-autocomplete
      .typesense-docsearch-suggestion
        .typesense-docsearch-suggestion--subcategory-column
          float none
          width 150px
          min-width 150px
          display table-cell
        .typesense-docsearch-suggestion--content
          float none
          display table-cell
          width 100%
          vertical-align top
        .ds-dropdown-menu
          min-width 515px !important

@media (max-width: $MQMobile)
  .typesense-search-wrapper
    .ds-dropdown-menu
      min-width calc(100vw - 4rem) !important
      max-width calc(100vw - 4rem) !important
    .typesense-docsearch-suggestion--wrapper
      padding 5px 7px 5px 5px !important
    .typesense-docsearch-suggestion--subcategory-column
      padding 0 !important
      background white !important
    .typesense-docsearch-suggestion--subcategory-column-text:after
      content " > "
      font-size 10px
      line-height 14.4px
      display inline-block
      width 5px
      margin -3px 3px 0
      vertical-align middle
</style>

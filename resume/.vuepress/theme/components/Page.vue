<template>
  <main class="page">
    <slot name="top"/>

    <Content class="theme-default-content content"/>
    <footer class="page-edit">
      <div
        class="edit-link"
        v-if="editLink"
      >
        <a
          :href="editLink"
          target="_blank"
          rel="noopener noreferrer"
        >{{ editLinkText }}</a>
        <OutboundLink/>
      </div>

      <div
        class="last-updated"
        v-if="lastUpdated"
      >
        <span class="prefix">{{ lastUpdatedText }}: </span>
        <span class="time">{{ lastUpdated }}</span>
      </div>
    </footer>

    <div class="page-nav" v-if="prev || next">
      <p class="inner">
        <span
          v-if="prev"
          class="prev"
        >
          ←
          <router-link
            v-if="prev"
            class="prev"
            :to="prev.path"
          >
            {{ prev.title || prev.path }}
          </router-link>
        </span>

        <span
          v-if="next"
          class="next"
        >
          <router-link
            v-if="next"
            :to="next.path"
          >
            {{ next.title || next.path }}
          </router-link>
          →
        </span>
      </p>
    </div>

    <slot name="bottom"/>
  </main>
</template>

<script>
import { resolvePage, outboundRE, endingSlashRE } from '../util'

export default {
  props: ['sidebarItems'],

  computed: {
    lastUpdated () {
      return this.$page.lastUpdated
    },

    lastUpdatedText () {
      if (typeof this.$themeLocaleConfig.lastUpdated === 'string') {
        return this.$themeLocaleConfig.lastUpdated
      }
      if (typeof this.$site.themeConfig.lastUpdated === 'string') {
        return this.$site.themeConfig.lastUpdated
      }
      return 'Last Updated'
    },

    prev () {
      const prev = this.$page.frontmatter.prev
      if (prev === false) {
        return
      } else if (prev) {
        return resolvePage(this.$site.pages, prev, this.$route.path)
      } else {
        return resolvePrev(this.$page, this.sidebarItems)
      }
    },

    next () {
      const next = this.$page.frontmatter.next
      if (next === false) {
        return
      } else if (next) {
        return resolvePage(this.$site.pages, next, this.$route.path)
      } else {
        return resolveNext(this.$page, this.sidebarItems)
      }
    },

    editLink () {
      if (this.$page.frontmatter.editLink === false) {
        return
      }
      const {
        repo,
        editLinks,
        docsDir = '',
        docsBranch = 'master',
        docsRepo = repo
      } = this.$site.themeConfig

      if (docsRepo && editLinks && this.$page.relativePath) {
        return this.createEditLink(repo, docsRepo, docsDir, docsBranch, this.$page.relativePath)
      }
    },

    editLinkText () {
      return (
        this.$themeLocaleConfig.editLinkText
        || this.$site.themeConfig.editLinkText
        || `Edit this page`
      )
    }
  },

  methods: {
    createEditLink (repo, docsRepo, docsDir, docsBranch, path) {
      const bitbucket = /bitbucket.org/
      if (bitbucket.test(repo)) {
        const base = outboundRE.test(docsRepo)
          ? docsRepo
          : repo
        return (
          base.replace(endingSlashRE, '')
           + `/src`
           + `/${docsBranch}/`
           + (docsDir ? docsDir.replace(endingSlashRE, '') + '/' : '')
           + path
           + `?mode=edit&spa=0&at=${docsBranch}&fileviewer=file-view-default`
        )
      }

      const base = outboundRE.test(docsRepo)
        ? docsRepo
        : `https://github.com/${docsRepo}`
      return (
        base.replace(endingSlashRE, '')
        + `/edit`
        + `/${docsBranch}/`
        + (docsDir ? docsDir.replace(endingSlashRE, '') + '/' : '')
        + path
      )
    }
  }
}

function resolvePrev (page, items) {
  return find(page, items, -1)
}

function resolveNext (page, items) {
  return find(page, items, 1)
}

function find (page, items, offset) {
  const res = []
  flatten(items, res)
  for (let i = 0; i < res.length; i++) {
    const cur = res[i]
    if (cur.type === 'page' && cur.path === decodeURIComponent(page.path)) {
      return res[i + offset]
    }
  }
}

function flatten (items, res) {
  for (let i = 0, l = items.length; i < l; i++) {
    if (items[i].type === 'group') {
      flatten(items[i].children || [], res)
    } else {
      res.push(items[i])
    }
  }
}

</script>

<style lang="stylus">
@require '../styles/config.styl'
.page {
  .content {
    font-size: $fontSize;
    font-family: menlo, pingfang;
    font-weight: $fontWeight;
    color: $textColor;

    * {
      margin: 0 !important;
    }

    > h1 {
      display: flex;
      align-items: center;

      > span {
        flex: 1;
        transform: translateY(-.2em)
      }

      > ul {
        float: right;
        font-size: 12px;
        font-weight: normal;
        margin: 0;
        padding: 0;

        > li {
          display: flex;
          flex-wrap: nowrap;

          > span {
            flex-shrink: 0;
            width: 6em;
            text-align right;

            &::after {
              content: ':';
              margin 0 0.25em;
            }
          }
        }
      }
    }

    > h2, > h3 {
      line-height: 1;
      border: 0;
      padding: 0;
      display: flex;
      align-items: baseline;
      margin-bottom: 0.6em !important;

      .header-anchor {
        display: none;
      }
    }

    > h2 {
      font-size: 1.35em;
      display: flex;

      &:not(:first-of-type) {
        margin-top: 1em !important;
      }

      &::after {
        content: '';
        flex: 1;
        border-bottom: 2px dashed #D3D3D3;
        margin-left: 10px;
      }
    }

    > h3 {
      font-size: 1.1em;

      &:not(:first-of-type) {
        margin-top: 0.6em !important;
      }
    }

    > p, > ul, > ol {
      line-height: 1.5;
    }

    > p {
    }

    > ul {
      margin: 0;
      margin-top: 0.5em;
    }

    > hr {
      margin-top: 0.4em !important;
      border-top: 1px dashed $borderColor;
    }

    .role {
      flex: 1;
      font-size: 12px;
      text-align: left;
    }

    .right {
      float: right;
      flex: 1;
      font-size: 0.9em;
      text-align: right;
      font-weight: normal;
      font-style: italic;
    }
  }
}
</style>

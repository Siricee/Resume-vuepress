<template>

    <span class="sidebar-footer">Powered by <a class="link" target="_blank" href="https://v1.vuepress.vuejs.org">VuePress</a></span>

</template>

<script>
    export default {
        name: "sidebarfooter"
    }
</script>

<style lang="stylus">
    .sidebar-footer
        position absolute
        bottom 2em
        padding-top: 0.25rem;
        padding-bottom: 0.25rem;
        border-left: none;
        padding-left: 2rem;
        display: inline-block;
        font-size: .8em;
        font-weight: 400;
        color: #2c3e50;
        border-left: 0.25rem solid transparent;
        line-height: 1.4;
        width: 100%;
        box-sizing: border-box;
        & .link
            color $accentColor
</style>
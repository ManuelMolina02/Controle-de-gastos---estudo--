<template>
<nav class="nav flex-column justify-content-between">
 <div>

 <router-link
 exact
 :key="i"
 exact-active-class="active"
 class="nav-link"
 :to="{name: router.name}"
 v-for="(router, i) in routerLinks"
 >
 <i class="fa" :class="`fa-${router.meta.icon}`"></i>
 {{ router.meta.title }}
 </router-link>

 <hr>

 <layout-new-expense />
 </div>

 <layout-logout/>

</nav>
</template>

<script>
import LayoutLogout from './LayoutLogout'
import LayoutNewExpense from './LayoutNewExpense'

export default {
  components: {
    LayoutLogout,
    LayoutNewExpense
  },
  computed: {
    routerLinks () {
      return this.$router.options.routes.filter(r => r.name !== 'login')
    }
  }
}
</script>

<style scoped  lang="scss">
  .nav {
      height: calc(100vh - 50px );
      width: calc(100% + 30px);
      margin-left:-15px;
  }
  .nav-link {
      color: var(--white);
      transition: .3s;
      &.active{
          color: var(--white);
          background-color: var(--featured-dark);
      }
      &:hover{
          color: var(--white);
          box-shadow:  0 0 14px var(--featured-dark);
          background-color: var(--featured);
      }
  }

</style>

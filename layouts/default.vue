<template>
  <div>
    <b-navbar type="dark" variant="primary">
      <b-navbar-brand href="#" @click="nav('/')" style="font-size:12px">아수라발발타</b-navbar-brand>

      <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

      <b-collapse id="nav-collapse" is-nav>
        <b-navbar-nav>
           <b-nav-item @click="nav('/auto')">자동추첨</b-nav-item>
          <b-nav-item @click="nav('/semiauto')">반자동추첨</b-nav-item>
          <b-nav-item @click="nav('/ai')">AI추첨</b-nav-item>
        </b-navbar-nav>
      </b-collapse>
    </b-navbar>
    <Nuxt />
  </div>
</template> 
<script>

export default {
  methods: {
    nav(name) {
      this.$router.push(name)
    }
  }
}
</script>
<style>

</style>

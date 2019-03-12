<template>
  <div class="container is-fullhd" style="height:100%;padding-right: 50px;padding-left: 50px;padding-top: 150px;background-color:#ffffff">
    <img src="@/assets/app_logo.png" style="height:150px">
    <h1 class="title has-text-dark">Rush</h1>
    <br>
    <a class="button is-rounded is-link is-medium is-fullwidth" @click="$router.push({name:'home'})">Use Rush on Website</a>
    <br>
    <button class="button is-rounded is-dark is-medium is-fullwidth" disabled @click="$router.push({name:'select', params:{group:group, name:name}})">Play Store (Soon)</button>
    <br>
    <button class="button is-rounded is-dark is-medium is-fullwidth" disabled @click="$router.push({name:'select', params:{group:group, name:name}})">App Store (Soon)</button>
  </div>
</template>

<script>
export default {
  name: 'download',
  data () {
    return {

    }
  }
}
</script>

<style scoped>
.dropdown.is-fullwidth {
  display: flex;
}
.dropdown.is-fullwidth > .dropdown-trigger, .dropdown.is-fullwidth > .dropdown-menu {
  width: 100%;
}

.navbar {
  background-color: #FDB849;
  height: 60px;
}
.navbar-brand {
  height: 60px;
  padding-left: 12px;
}
.logo {
  width: 35px;
  height: 35px !important;
}
.navbar-menu {
  margin-right: 0px !important;
  margin-left: 0px;
}
.navbar-end {
  padding-right: 12px;
}
.box {
  padding-right: 20px;
  padding-left: 20px;
}
</style>

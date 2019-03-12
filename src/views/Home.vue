<template>
  <div class="container is-fullhd" style="height:100%;">
    <div :style="bg1"></div>
    <nav class="navbar" role="navigation" aria-label="main navigation">
      <div class="navbar-brand">
        <a class="navbar-item">
          <img src="@/assets/RUSHlogo.png" alt="logo" class="logo">
        </a>
      </div>
      <div class="navbar-menu">
        <div class="navbar-end">
          <div class="navbar-item">
            <div class="button is-rounded is-dark is-inverted is-outlined" disabled>
                Log in
            </div>
          </div>
        </div>
      </div>
    </nav>
    <div class="columns">
      <div class="column has-text-left is-offset-1 is-10">
        <br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br>
        <p style="margin-bottom: 3px;">select store to go :{{selected}}</p>

<div :class="'dropdown is-up is-fullwidth ' + (select_active )">
  <div class="dropdown-trigger is-round">
    <button class="button is-fullwidth" aria-haspopup="true" aria-controls="dropdown-menu" @click="drop_click">
      <span>{{selected_store}}</span>
      <span class="icon is-small">
        <i class="fas fa-angle-down" aria-hidden="true"></i>
      </span>
    </button>
  </div>
  <div class="dropdown-menu" id="dropdown-menu" role="menu">
    <div class="dropdown-content">
      <a class="dropdown-item" v-for="(store, index) in stores" :key="index" @click="selectName(store)">
        {{store.name}}
      </a>
    </div>
  </div>
</div>

        <div class="columns">
          <div class="column is-8 is-offset-2">
          <button class="button is-rounded is-dark is-fullwidth" @click="$router.push({name:'select', params:{group:group, name:name, selected:' '}})">Next</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'home',
  data () {
    return {
      nameSuper: '',
      Image: 'https://www.orcam.com/wp-content/uploads/2014/08/supermarket-960x480.jpg',
      nameOfSupermarket: [],
      stores: [],
      chooseName: 'AAAA',
      mapData: null,
      selected: null,
      select_active: '',
      selected_store: 'Select store...'
    }
  },
  computed: {
    bg1: function () {
      return {
        backgroundImage: 'url(\'' + this.Image + '\')',
        backgroundSize: 'cover',
        backgroundPosition: 'center',
        height: '100vh',
        width: '100vw',
        zIndex: '0',
        top: '0px',
        position: 'absolute',
        filter: 'grayscale(100%)',
        opacity: '0.4'
      }
    },
    searchingStore: function () {
      let output = []
      this.nameOfSupermarket = this.stores
      console.log(this.nameOfSupermarket)
      for (const key in this.nameOfSupermarket) {
        if (this.nameOfSupermarket.hasOwnProperty(key)) {
          const element = this.nameOfSupermarket[key]
          element.search(this.nameSuper)
          if (element.search(new RegExp(this.nameSuper, 'i')) >= 0) {
            output.push(element)
          }
        }
      }
      return output
    }
  },
  methods: {
    selectName: function (store) {
      this.nameSuper = store.name
      this.chooseName = store.name
      this.name = store.name
      this.group = store.group
      this.selected_store = store.name
      this.select_active = ''
      console.log('what')
    },
    drop_click () {
      if (this.select_active == '') {
        this.select_active = 'is-active'
      } else {
        this.select_active = ''
      }
    }
  },
  mounted: function() {
    var url = "http://localhost:8000/stores"
    this.$http.get(url)
    .then(response => {
        console.log(response.data)
        this.stores = response.data.map
    })
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
</style>

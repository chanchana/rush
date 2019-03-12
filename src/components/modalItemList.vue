<template>
  <div :class="'modal' + (isOpen ? ' is-active' : '')">
    <div class="modal-background" style="background-color: #FFFFFF; opacity: 0.75;"></div>
     <div class="modal-card" style="padding: 0px 12px;">
      <header class="modal-card-head" style="background-color: #27364B;">
        <h5 class="modal-card-title is-5 has-text-light">Item List</h5>
        <button class="delete" aria-label="close" @click="closeModal"></button>
      </header>
      <section class="modal-card-body" style="background-color: #27364B;">
        <div class="box" style="padding: 12px; overflow-y: scroll; height: 50vh; background-color: #FFFFFF;">
          <div class="box has-text-left" style="padding: 12px 18px; background-color: #EFEFEF; font-weight: 500;"
          v-for="(item, index) in itemList" :key="index">
            <div class="columns is-mobile">
              <div class="column is-11">
                {{ item.Brand }}
              </div>
              <div class="column is-1">
                <i class="fas fa-trash" @click="deleteList(item)"></i>
              </div>
            </div>
          </div>
        </div>
        <div class="columns">
          <div class="column has-text-left has-text-light is-7">
            total: {{ itemList.length }}
          </div>
          <div class="column has-text-right is-5">
            <button class="button is-rounded" :disabled="itemList.length <= 0" @click="gotoRoute">Get Route</button>
          </div>
        </div>
      </section>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      isOpen: false
    }
  },
  methods: {
    openModal: function () {
      this.isOpen = true
    },
    closeModal: function () {
      this.isOpen = false
    },
    deleteList: function (value) {
      console.log('deletelist', value)
      this.$emit('deleteFromList', value)
    },
    gotoRoute: function () {
      // let link = 'https://rush.serveo.net/demo/map/sample/sample/route/'
      // console.log(this.$route.params.group)
      let link = '/map/'+this.$route.params.group+'/'+this.$route.params.name+'/route/'
      let route = ''
      console.log('TESTTT' + this.itemId)
      for (var i = 0; i < this.itemId.length; i++) {
        console.log(i + '=========>' +  this.itemId[i])
        if (this.itemId[i] != '') {
          route += this.itemId[i]
          if (i < this.itemId.length - 1) {
            route += ';'
          }
        }
      }
      // for (const key in this.itemId) {
      //   if (this.itemId.hasOwnProperty(key)) {
      //     const element = this.itemList[key]
      //     if (route === '') route = element
      //     else route += ';' + element
      //   }
      // }
      // window.location.href = link + route

      this.$router.push(link+route)
    }
  },
  props: ['category', 'itemList', 'itemId']
}
</script>

<style>
.modal-card-head {
  border-bottom: 0px !important;
}

.modal-card-body {
  border-bottom-left-radius: 6px;
  border-bottom-right-radius: 6px;
}

</style>

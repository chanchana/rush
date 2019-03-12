<template>
  <div class="container is-fullhd" style="background-color: #EEEEEE;">
    <nav class="navbar" role="navigation" aria-label="main navigation">
      <div class="navbar-menu">
        <div class="navbar-start">
          <div class="navbar-item">
            <a v-if="search.length == 0" class="button is-text is-myBack" @click="$router.push({name: 'select', params:{group:group, name:name, selected:item_url}})"><span class="icon"><i class="fas fa-chevron-left"></i></span></a>
            
            <a v-else class="button is-text is-myBack" @click="clear_search">
              <span class="icon">
                <i class="fas fa-times"></i>
              </span>
            </a>
            <h5 class="subtitle is-5 has-text-dark has-text-weight-bold"> Select Merchandises</h5>
          </div>
        </div>
        <div class="navbar-end">
          <div class="navbar-item">
          <button class="button is-rounded is-light is-inverted badge is-badge-danger is-badge-medium" @click="openItemList"
           :data-badge="itemList.length">Item{{ itemList.length >= 2 ? 's' : '' }}</button>
          </div>
        </div>
      </div>
    </nav>
    <br><br><br>
     <div class="columns">
      <div class="column columnPadding">
        <div class="control has-icons-right">
          <input class="input is-rounded" type="text" placeholder="Search" v-model="search">
          <span class="icon is-small is-right">
            <i class="fas fa-search"></i>
          </span>
        </div>
      </div>
    </div>

    <div v-if="search.length > 0">
      <div
        class="columns"
        v-for="(item, index) in search_items"
        :key="index"
        style="padding-left: 30px; padding-right: 30px; margin-left:70px"
      >
        <button v-if="isSelected(item.ID)"  class="button is-fullwidth is-medium" style="background-color: #DAD6D6;" @click="deleteMe(item)">
          <span class="icon">
            <i class="fas fa-check"></i>
          </span>
          <span><h6 class="subtitle is-6 has-text-weight-bold">{{item.Brand}}</h6></span>
        </button>
        <button v-else-if="item.ID != null" class="button is-fullwidth is-medium " @click="addToList(item)">
          <h6 class="subtitle is-6">{{item.Brand}}</h6>
        </button>
        <button v-else disabled class="button is-fullwidth is-medium ">
          <h6 class="subtitle is-6">{{item.Brand}}</h6>
        </button>
      </div>
    </div>

    <div v-if="search.length == 0">
      <div class="columns">
        <div class="column columnPadding">
          <h6 class="subtitle is-10 has-text-dark has-text-center">Items</h6>
        </div>
      </div>


      <div v-if="!is_small">
        <div class="columns" v-for="(aitem, index) in allitems" :key="index" style="padding-left: 18px; padding-right: 18px;">
          <div class="column is-half">
            <button v-if="isSelected(aitem.left.ID)" class="button is-fullwidth is-medium" style="background-color: #DAD6D6;" @click="deleteMe(aitem.left)">
              <h6 class="subtitle is-10 has-text-weight-bold"> <i class="fas fa-check"></i>  {{ aitem.left.Brand }}</h6>
            </button>
            <button v-else class="button is-fullwidth is-medium" @click="addToList(aitem.left)">
              <h6 class="subtitle is-10">{{ aitem.left.Brand }}</h6>
            </button>
          </div>
          <div class="column is-half" v-if="aitem.right !== null">
            <button v-if="isSelected(aitem.right.ID)" class="button is-fullwidth is-medium" style="background-color: #DAD6D6;" @click="deleteMe(aitem.right)">
              <h6 class="subtitle is-10 has-text-weight-bold"> <i class="fas fa-check"></i>  {{ aitem.right.Brand }}</h6>
            </button>
            <button v-else class="button is-fullwidth is-medium" @click="addToList(aitem.right)">
              <h6 class="subtitle is-10">{{ aitem.right.Brand }}</h6>
            </button>
          </div>
        </div>
      </div>

      <div v-else>
        <div class="columns" v-for="(item, index) in items" :key="index" style="padding-left: 18px; padding-right: 18px;">
          <div class="column is-12">
            <button v-if="isSelected(item.ID)" class="button is-fullwidth is-medium" style="background-color: #DAD6D6;" @click="deleteMe(item)">
              <h6 class="subtitle is-10 has-text-weight-bold"> <i class="fas fa-check"></i>  {{ item.Brand }}</h6>
            </button>
            <button v-else class="button is-fullwidth is-medium" @click="addToList(item)">
              <h6 class="subtitle is-10">{{ item.Brand }}</h6>
            </button>
          </div>
        </div>
      </div>


    </div>

    <modal-select-brand ref="modalSelectBrand"/>
    <modal-item-list ref="modalItemList" :itemId="itemId" :category="category" :itemList="itemList" @deleteFromList="deleteMe"/>
  </div>
</template>

<script>
import modalSelectBrand from '@/components/modalSelectBrand'
import modalItemList from '@/components/modalItemList'
export default {
  components: {
    'modal-select-brand': modalSelectBrand,
    'modal-item-list': modalItemList
  },
  created() {
    this.group = this.$route.params.group
    this.name = this.$route.params.name
    window.addEventListener('resize', this.handleResize)
    this.handleResize();
  },
  destroyed() {
    window.removeEventListener('resize', this.handleResize)
  },
  data () {
    return {
      // category: ['Foods', 'Drinks', 'Fruits', 'Vegetables', 'Pets', 'Babies', 'Sport&Travels'],
      category: { '1': 'ขนม', '2': 'Curtains', '3': 'Rug', '4': 'T-Shirt', '5': 'Skirt', '6': 'Underwear', '7': 'Pets', '8': 'Camera', '9': 'Fresh milk', '10': 'Bread', '11': 'Cheese', '12': 'Tea', '13': 'Butter', '14': 'Beef', '15': 'Pork', '16': 'Coffee', '17': 'Salt', '18': 'Pepper', '19': 'Vinegar', '20': 'Salad', '21': 'Spice', '22': 'Vegetable', '23': 'Noodle', '24': 'Jelly', '25': 'Canfood', '26': 'Seafood', '27': 'Bag', '28': 'Chocolate', '29': 'Macaroni', '30': 'Beer', '31': 'Wheat', '32': 'Cam', '33': 'Oat', '34': 'Sweets', '35': 'Snack', '36': 'Cardboard', '37': 'Pen', '38': 'Pencil', '39': 'Oil', '40': 'Sugar', '41': 'Bandage', '42': 'Sauce', '43': 'Light', '44': 'Phone', '45': 'Towel', '46': 'Book', '47': 'Toilet paper', '48': 'Glasses', '49': 'Paper', '50': 'Blotting paper', '51': 'colored paper', '52': 'Color pencil', '53': 'Marker', '54': 'Highlight', '55': 'Ballpoint pen', '56': 'Coloring pen' },
      itemList: [],
      itemId: [],
      items: [],
      search: '',
      group: '',
      name: '',
      window: {
        width: 0,
        height: 0
      }
    }
  },
  computed: {
    item_url() {
      if (this.itemId.length == 0) {
        return ' '
      }else {
        return this.itemId.join(';')
      }
    },
    allitems: function () {
      let output = []
      var count = 0
      while (count < this.items.length) {
        if (count%2 == 0) {
          output.push(
            {
              left : this.items[count],
              right : null
            }
          )
        }
        else {
          output[output.length - 1].right = this.items[count]
        }
        count += 1
      }
      return output
    },
    is_small () {
      if (this.window.width < 700) {
        console.log(this.window.width)
        return true
      }
      return false
    },
    search_items: function() {
      var output = []

      console.log(this.search)

      var items = this.items
      for (var i = 0; i < items.length; i++) {
        var c1 = String(items[i].Name).toLowerCase()
        var c2 = String(items[i].Brand).toLowerCase()
        if (c1.includes(this.search.toLowerCase()) || c2.includes(this.search.toLowerCase())) {
          output.push(items[i])
        }
      }


      if (output.length == 0) {
        output.push({'ID':null, 'Brand':'No Result'})
      }
      return output
    }
  },
  methods: {
    isSelected: function(itemid) {
      // console.log(this.itemId)
      // console.log(itemid + ' ===== ')
      for (var i = 0; i < this.itemId.length; i++){
        // console.log(itemid + ' ==????== ' + this.itemId[i])
        if (itemid == this.itemId[i]) {
          // console.log('YESS')
          return true
        }
      }
      return false
    },
    clear_search: function() {
      this.search = ''
    },
    selectItem: function (name) {
      console.log(name)
      this.$refs.modalSelectBrand.openModal(name)
    },
    selectFakeItem: function (id) {
      console.log(name)
      // this.$refs.modalSelectBrand.openModal(name)
      if (this.itemList.indexOf(id) >= 0) return ''
      this.itemList.push(id)
    },
    openItemList: function () {
      this.$refs.modalItemList.openModal()
    },
    addToList: function(item) {
      console.log(item.ID + ' <--- ID   Brand ---->  ' + item.Brand + '')
      this.itemId.push(item.ID);
      this.itemList.push(item);
    },
    deleteMe: function(item) {
      this.itemId.splice(this.itemId.indexOf(item.ID), 1)
      this.itemList.splice(this.itemList.indexOf(item), 1)
    },
    handleResize() {
      this.window.width = window.innerWidth;
      this.window.height = window.innerHeight;
    }
  },
  mounted: function() {
    var url = "http://178.128.24.70:8000/stores/"+this.group+"/"+this.name
    this.$http.get(url)
    .then(response => {
      console.log(response.data)
      this.items = response.data.data

      this.itemId = this.$route.params.selected.split(";")
      for (var i = 0; i < this.itemId.length; i++) {
        for (var j = 0; j < this.items.length; j++) {
          if (this.itemId[i] == this.items[j].ID) {
            this.itemList.push(this.items[j]);
          }
        }
      }
    })
  }
}
</script>

<style scoped>
.navbar {
  background-color: #FDB849;
  height: 60px;
  position: fixed;
  overflow: hidden;
  width: 100%;
  top: 0;
}
.navbar-menu {
  margin-right: 0px !important;
  margin-left: 0px;
}
.navbar-start {
  padding-left: 12px;
}
.navbar-end {
  padding-right: 10px;
}
.marginsTop {
  margin-top: 18px !important;
}
.columnPadding {
  padding: 18px 24px 0px 24px;
}
.canClick {
  cursor: pointer;
}
.icon-item {
  width: 40px;
  height: 40px;
}
.box {
  padding: 18px;
}
.is-myBack {
  text-decoration: none !important;
  margin-right: 6px;
}
</style>

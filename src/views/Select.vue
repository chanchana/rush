<template>
  <div class="container is-fullhd" style="background-color: #EEEEEE;">
    <nav class="navbar" role="navigation" aria-label="main navigation">
      <div class="navbar-menu">
        <div class="navbar-start">
          <div class="navbar-item">

            <a v-if="search.length == 0" class="button is-text is-myBack" @click="$router.push('/')">
              <span class="icon">
                <i class="fas fa-chevron-left"></i>
              </span>
            </a>

            <a v-else class="button is-text is-myBack" @click="clear_search">
              <span class="icon">
                <i class="fas fa-times"></i>
              </span>
            </a>

            <h5 class="subtitle is-5 has-text-dark has-text-weight-bold">Select Merchandises</h5>
          </div>
        </div>
        <div class="navbar-end">
          <div class="navbar-item">
            <button
              class="button is-rounded is-light is-inverted badge is-badge-danger is-badge-medium"
              @click="openItemList"
              :data-badge="itemList.length"
            >Item{{ itemList.length >= 2 ? 's' : '' }}</button>
          </div>
        </div>
      </div>
    </nav>

    <br>
    <br>
    <br>
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

    <div v-else>
      <div class="column columnPadding">
        <figure class="image is-3by1 marginsTop canClick" @click="openPromotion">
          <img src="@/assets/promo1.png">
        </figure>
        <h6 class="subtitle marginsTop is-5 has-text-dark has-text-left">Frequently</h6>
      </div>
      <!-- </div> -->

      <div class="columns is-variable is-2" style="padding-left: 18px; padding-right: 18px;">
        <div v-for="(item, index) in frequently1" :key="index" class="column">
          <div v-if="isSelected(item.ID)" class="box canClick has-text-weight-bold" style="background-color: #DAD6D6;" @click="deleteMe(item)">
            <img :src="require('@/assets/' + item.ID + '.png')"  class="icon-item">
            <br><i class="fas fa-check"></i>{{item.Brand}}
          </div>
          <div v-else class="box canClick" @click="addToList(item)">
            <img :src="require('@/assets/' + item.ID + '.png')" class="icon-item">
            <br>{{item.Brand}}
          </div>
        </div>
      </div>

      <div class="columns is-variable is-2" style="padding-left: 18px; padding-right: 18px;">
        <div v-for="(item, index) in frequently2" :key="index" class="column">
          <div v-if="isSelected(item.ID)" class="box canClick has-text-weight-bold" style="background-color: #DAD6D6;" @click="deleteMe(item)">
            <img :src="require('@/assets/' + item.ID + '.png')" class="icon-item">
            <br><i class="fas fa-check"></i> {{item.Brand}}
          </div>
          <div v-else class="box canClick" @click="addToList(item)">
            <img :src="require('@/assets/' + item.ID + '.png')" class="icon-item">
            <br>{{item.Brand}}
          </div>
        </div>
      </div>

      <div class="columns">
        <div class="column columnPadding">
          <h6 class="subtitle marginsTop is-5 has-text-dark has-text-left">Category</h6>
        </div>
      </div>
      <div
        class="columns"
        v-for="(cat, index) in category"
        :key="index"
        style="padding-left: 18px; padding-right: 18px;"
      >
        <button
          class="button is-fullwidth is-medium marginsTop"
          @click="$router.push({name:'select_category', params:{group:group, name:name, selected:item_url , category:cat}})
  "
        >
          <h6 class="subtitle is-5">{{ cat }}</h6>
        </button>
      </div>
      <div class="columns">
        <div class="column columnPadding">
          <h6 class="subtitle is-6 has-text-dark">or</h6>
          <button
            class="button is-fullwidth is-medium is-light"
            style="background-color: #CBCBCB;"
            @click="$router.push({name:'allitems', params:{group:group, name:name, selected:' ' + itemId.join(';')}})"
          >
            <h6 class="subtitle is-6">All Items</h6>
          </button>
          <br>
        </div>
      </div>
    </div>
    <modal-select-brand ref="modalSelectBrand"/>
    <modal-item-list
      ref="modalItemList"
      :category="category"
      :itemList="itemList"
      :itemId="itemId"
      @deleteFromList="deleteMe"
    />
    <modal-promotion ref="modalPromotion"/>
  </div>
</template>

<script>
import modalSelectBrand from "@/components/modalSelectBrand";
import modalItemList from "@/components/modalItemList";
import modalPromotion from "@/components/modalPromotion";
export default {
  components: {
    "modal-select-brand": modalSelectBrand,
    "modal-item-list": modalItemList,
    "modal-promotion": modalPromotion
  },
  created() {
    this.group = this.$route.params.group;
    this.name = this.$route.params.name;
  },
  data() {
    return {
      category: [],
      items: [],
      itemList: [],
      itemId: [],
      frequently1: [],
      frequently2: [],
      searchItems: "",
      search: "",
      group: "",
      name: "",
      search_active: "is-active"
    };
  },
  computed: {
    item_url() {
      if (this.itemId.length == 0) {
        return ' '
      }else {
        return this.itemId.join(';')
      }
    },
    categoryloop: function(id) {
      return '@/assets/' + id + '.png'
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
    addToList: function(item) {
      console.log(item.ID + ' <--- ID   Brand ---->  ' + item.Brand + '')
      this.itemId.push(item.ID);
      this.itemList.push(item);
    },
    clear_search: function() {
      this.search = ''
    },
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
    selectItem: function(name) {
      console.log(name);
      this.$refs.modalSelectBrand.openModal(name);
    },
    selectFakeItem: function(id) {
      console.log(name);
      // this.$refs.modalSelectBrand.openModal(name)
      if (this.itemList.indexOf(id) >= 0) return "";
      this.itemList.push(id);
    },
    openItemList: function() {
      this.$refs.modalItemList.openModal();
    },
    openPromotion: function() {
      this.$refs.modalPromotion.openModal();
    },
    deleteMe: function(item) {
      this.itemId.splice(this.itemId.indexOf(item.ID), 1)
      this.itemList.splice(this.itemList.indexOf(item), 1)
    },
    click_category: function(name) {
      this.$route.push({
        name: "select_category",
        params: {
          group: this.group,
          name: this.name,
          selected: this.itemList,
          category: name
        }
      });
    }
  },
  mounted: function() {
    var url = "http://localhost:8000/stores/" + this.group + "/" + this.name;
    this.$http.get(url).then(response => {
      console.log(response.data);
      this.category = response.data.category;
      this.items = response.data.data;
      console.log(this.items);

      this.items = this.items.sort(function (a, b) {
        return b.Clicked - a.Clicked
      })

      this.frequently1 = this.items.slice(0, 3)
      this.frequently2 = this.items.slice(3, 6)

      this.itemId = this.$route.params.selected.split(";")
      for (var i = 0; i < this.itemId.length; i++) {
        for (var j = 0; j < this.items.length; j++) {
          if (this.itemId[i] == this.items[j].ID) {
            this.itemList.push(this.items[j]);
          }
        }
      }
    });
  }
};
</script>

<style scoped>
.navbar {
  background-color: #fdb849;
  height: 60px;
  position: fixed;
  overflow: hidden;
  width: 100%;
  top: 0;
}
/* #fdb849 */

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

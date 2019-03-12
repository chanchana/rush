<template>
  <div class="container is-fullhd" style="background-color: #EEEEEE;">
    <nav class="navbar" role="navigation" aria-label="main navigation">
      <div class="navbar-menu">
        <div class="navbar-start">
          <div class="navbar-item">

            <a v-if="search.length == 0" class="button is-text is-myBack" @click="$router.push({name:'select', params:{group:group, name:name, selected:item_url}})">
              <span class="icon">
                <i class="fas fa-chevron-left"></i>
              </span>
            </a>

            <a v-else class="button is-text is-myBack" @click="clear_search">
              <span class="icon">
                <i class="fas fa-times"></i>
              </span>
            </a>

            <h5 class="subtitle is-5 has-text-dark has-text-weight-bold">{{category}}</h5>
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
      <div class="column">
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
        <button v-if="isSelected(item.ID)" class="button is-fullwidth is-medium" style="background-color: #DAD6D6;" @click="deleteMe(item)">
          <span class="icon">
            <i class="fas fa-check"></i>
          </span>
          <span><h6 class="subtitle is-5 has-text-weight-bold">{{item.Brand}}</h6></span>
        </button>
        <button v-else-if="item.ID != null" class="button is-fullwidth is-medium " @click="addToList(item)">
          <h6 class="subtitle is-6">{{item.Brand}} {{item.Clicked}}</h6>
        </button>
        <button v-else disabled class="button is-fullwidth is-medium ">
          <h6 class="subtitle is-6">{{item.Brand}}</h6>
        </button>
      </div>
    </div>

    <div v-if="search.length == 0">
      <div class="column columnPadding">
        <h6 class="subtitle marginsTop is-5 has-text-dark has-text-left">Frequently</h6>
      </div>
    <!-- <div class="columns is-variable is-2" style="padding-left: 18px; padding-right: 18px;">
      <div v-for="(item, index) in frequently" :key="index" class="column">
          <div class="box canClick" @click.once="selectFakeItem('B1001')">
            <img src="@/assets/book.png" class="icon-item">
            <br>{{item.Brand}}
          </div>
        </div>
    </div> -->

    <div class="columns is-variable is-1" style="padding-left: 18px; padding-right: 18px;">
        <div v-for="(item, index) in frequently" :key="index" class="column">
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
        <h6 class="subtitle marginsTop is-5 has-text-dark has-text-left">Items</h6>
      </div>
    </div>

    <div
      class="columns"
      v-for="(item, index) in items"
      :key="index"
      style="padding-left: 18px; padding-right: 18px;"
    >
      <!-- <button v-if="isSelected(item.ID)" disabled class="button is-fullwidth is-medium marginsTop"  @click="addToList(item)">
        <span><h6 class="subtitle is-6">{{ item.Brand }}</h6></span>
        <span class="icon has-text-success">
          <i class="fas fa-check-square"></i>
        </span>
      </button>
      <button v-else class="button is-fullwidth is-medium marginsTop"  @click="addToList(item)">
        <h6 class="subtitle is-6">{{ item.Brand }}</h6>
      </button> -->
      <button v-if="isSelected(item.ID)"  class="button is-fullwidth is-medium marginsTop" style="background-color: #DAD6D6;" @click="deleteMe(item)">
          <span class="icon">
            <i class="fas fa-check"></i>
          </span>
          <span><h6 class="subtitle is-5 has-text-weight-bold">{{item.Brand}}</h6></span>
        </button>
        <button v-else class="button is-fullwidth is-medium marginsTop" @click="addToList(item)">
          <h6 class="subtitle is-5">{{item.Brand}}</h6>
        </button>
    </div>
    </div>

    <modal-item-list
      ref="modalItemList"
      :category="category"
      :itemList="itemList"
      :itemId="itemId"
      @deleteFromList="deleteMe"
    />
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
    this.category = this.$route.params.category;
    console.log(this.category);
  },
  data() {
    return {
      category: "",
      items: [],
      itemId: [],
      itemList: [],
      frequently: [],
      item_count: 0,
      search: "",
      group: "",
      name: ""
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
      console.log(this.itemId)
      console.log(itemid + ' ===== ')
      for (var i = 0; i < this.itemId.length; i++){
        console.log(itemid + ' ==????== ' + this.itemId[i])
        if (itemid == this.itemId[i]) {
          console.log('YESS')
          return true
        }
      }
      return false
    },
    clear_search: function() {
      this.search = ''
    },
    selectItem: function(name) {
      console.log(name);
      this.$refs.modalSelectBrand.openModal(name);
    },
    selectFakeItem: function(id) {
      console.log(name);
      if (this.itemList.indexOf(id) >= 0) return "";
      this.itemList.push(id);
    },
    openItemList: function() {
      this.$refs.modalItemList.openModal();
    },
    openPromotion: function() {
      this.$refs.modalPromotion.openModal();
    },
    addToList: function(item) {
      this.itemId.push(item.ID);
      this.itemList.push(item);
    },
    deleteMe: function(item) {
      this.itemId.splice(this.itemId.indexOf(item.ID), 1)
      this.itemList.splice(this.itemList.indexOf(item), 1)
    }
  },
  mounted: function() {
    var url =
      "http://178.128.24.70:8000/stores/" +
      this.group +
      "/" +
      this.name +
      "/" +
      this.category;
    this.$http.get(url).then(response => {
      this.items = response.data.data;
      this.item_count = this.items.length;
      console.log(this.items);

      this.items = this.items.sort(function (a, b) {
        return b.Clicked - a.Clicked
      })

      this.frequently = this.items.slice(0, 3)

      var url = "http://178.128.24.70:8000/stores/" + this.group + "/" + this.name;
      this.$http.get(url).then(response => {
        var allitems = response.data.data;
        console.log(this.items);

        this.itemId = this.$route.params.selected.split(";");
        for (var i = 0; i < this.itemId.length; i++) {
          for (var j = 0; j < allitems.length; j++) {
            if (this.itemId[i] == allitems[j].ID) {
              this.itemList.push(allitems[j]);
            }
          }
        }
      });
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

<template>
<div :class="'modal' + (isOpen ? ' is-active' : '')">
  <div class="modal-background" style="background-color: #FFFFFF; opacity: 0.75;"></div>
  <div class="modal-card" style="padding: 0px 10px;">
    <header class="modal-card-head" style="background-color: #27364B;">
      <p class="modal-card-title is-5 has-text-light">Crazy Sale</p>
      <button class="delete" aria-label="close" @click="closeModal"></button>
    </header>
    <section class="modal-card-body" style="background-color: #27364B;">
      <div class="box" style="padding: 12px; overflow-y: scroll; height: 50vh; background-color: #FFFFFF; margin-top: 10px;">
        <!-- <div class="message-body has-text-left" style="border-radius: 0px; border-style: hidden;"> -->
          <p class="has-text-left">
            Grand Opening Sale for every item in Bottom Maket! Get the lowest price of every item in this universe. OMG! first come first serve najaaa. The new supermarket that is bigger than this planet, Bottom Market!, is now open. I'm gonna repeat the text to make it look more real! Grand Opening Sale for every item in Bottom Maket! Get the lowest price of every item in this universe. OMG! first come first serve najaaa.
          <br><br>
          <strong>Privileges for new account:</strong> 
            Clebrate these weekend by opening new Bottom Market SSVIP for free! and get the even more lower price. Go now and shop until you broke!
            <br><br>
            For more information : bottommarket.com, 
            tel: 191
          </p>
        <!-- </div> -->
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
    }
  }
}
</script>

<style scoped>
.modal-card-head {
  border-bottom: 0px !important;
}

.modal-card-body {
  border-bottom-left-radius: 6px;
  border-bottom-right-radius: 6px;
}
</style>


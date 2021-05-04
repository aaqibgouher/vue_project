<template>
  <div class="home container">
    <b-button v-b-modal.modal-1>Launch demo modal</b-button>

    <b-modal id="modal-1" title="BootstrapVue">
      <p class="my-4">Hello from modal!</p>
    </b-modal>
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: "Home",
  components: {
    
  }
};
</script>

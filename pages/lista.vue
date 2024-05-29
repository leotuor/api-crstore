<template>
  <v-row>
    <v-col>
      <v-card
        v-for="(usuario, i) in items"
        :key="i"
        class="ma-5"
        :title="usuario.login"
        :subtitle="usuario.email"
        @click="openWindow(usuario)"
        >
        <v-avatar size="60">
          <img
            alt="John"
            :src="usuario.profileImage"
            width="60"
          >
        </v-avatar>
      </v-card>
    </v-col>
  </v-row>
</template>
<script>
export default {
  name: 'PaginaLista',
  data () {
    return {
      items: [],
      usuario: {
        id: null,
        nome: null,
        email: null,
        login: null,
        password: null,
        idCargo: null,
        profileImage: null,
      }, 
    }
    
  },

  async created() {
    await this.getItems();
  },
  methods: {
    async getItems() {
      const response = await this.$api.get("/usuario");
      this.items = response.data;
    },
    openWindow(usuario){
      console.log(usuario);
      window.location.href = `${usuario.profileImage}`
    }
  }
}
</script>
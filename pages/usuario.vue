<!-- eslint-disable vue/valid-v-slot -->
<template v-slot:activator="{ usuario }">
  <v-container class="justify-center mt-5">
    <v-row>
      <v-col>
        <v-text-field
          v-model="usuario.name"
          variant="outlined"
          label="Nome"
          placeholder="Nome"
        />
        <v-text-field
          v-model="usuario.username"
          variant="outlined"
          label="Nome de Usuario"
          placeholder="Nome de Usuario"
        />
        <v-text-field
          v-model="usuario.email"
          variant="outlined"
          label="Email"
          placeholder="Email"
        />
        <v-text-field
          v-model="usuario.phone"
          variant="outlined"
          label="Numero de Telefone"
          placeholder="Numero de Telefone"
        />
      </v-col>
    </v-row>
    <v-spacer/>
    <v-btn color="green" variant="outlined" @click="persist()">
      <v-icon>mdi-check</v-icon>
    </v-btn>
  </v-container>
</template>

<script>
export default {
  data: () => {
    return {
      dialog: false,
      usuario: {
        id: null,
        username: null,
        cpf: null,
        name: null,
        phone: null,
        passwordHash: null,
        token: null,
        role: null,
        cart: null,
        email: null,
        recuperation: null,
      },
      items: [],
    };
  },


  async created() {
    await this.getItems();
  },

  methods: {
    resetUsuario() {
      this.usuario = {
        id: null,
        username: null,
        cpf: null,
        name: null,
        phone: null,
        passwordHash: null,
        token: null,
        role: null,
        cart: null,
        email: null,
        recuperation: null,
      };
      this.ativo = false;
    },

    async persist() {
      if (this.usuario.id) {
        const response = await this.$api.post(
          `/usuario/persist/${this.usuario.id}`,
          this.usuario
        );
      }
      this.resetUsuario();
      await this.getItems();
    },

    editItem(items) {
      this.usuario = {
        ...items,
      };
      this.ativo = true;
    },

    watch: {
      ativo(valor) {
        if (valor == false) {
          this.resetUsuario();
        }
      },
    },

    async getItems() {
      const response = await this.$api.get("/usuario");
      this.items = response.data;
    },

  },
};
</script>
<!-- eslint-disable vue/valid-v-slot -->
<template>
  <v-container class="justify-center mt-5">
    <TabelaDados
      titulo="Tabela Produtos"
      :headers="headers"
      :items="items"
      @editItem="editItem"
      @deletarItem="deletarItem"
      @abrir-dialog="() => ativo = true"
    />
    <v-dialog v-model="ativo" max-width="500">
      <v-card height="600" width="700">
        <v-card-title>
          <h1>{{ tituloDialog }} um Evento</h1>
        </v-card-title>
        <v-card-text>
          <v-row>
            <v-col>
              <v-text-field
                v-model="produto.name"
                variant="outlined"
                label="Nome"
                placeholder="Nome"
              />
              <v-text-field
                v-model="produto.price"
                variant="outlined"
                label="Preço"
                placeholder="Preço"
              />
              <v-text-field
                v-model="produto.description"
                variant="outlined"
                label="Description"
                placeholder="Description"
              />
              <v-autocomplete
                v-model="produto.idCategory"
                :items="categorias"
                items-title="name"
                items-value="id"
              />
              <v-text-field
                v-model="produto.image"
                variant="outlined"
                label="Miniatura"
                placeholder="Miniatura"
              />
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn color="green" variant="outlined" @click="persist()">
            <v-icon> mdi-check </v-icon>
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
export default {
  data: () => {
    return {
      ativo: false,
      loading: true,
      dialog: false,
      textoproduto: null,
      categorias: [],
      produto: {
        id: null,
        name: null,
        price: null,
        image: null,
        description: null,
        idCategory: null,
      },
      items: [],
      headers: [
        {
          title: "Identificador",
          key: "id",
        },
        {
          title: "nome",
          key: "name",
        },
        {
          title: "Descrição",
          key: "description",
        },
        {
          title: "actions",
          key: "actions",
          sortable: false,
        },
      ],
    };
  },

  computed: {
    tituloDialog: function () {
      return this.produto.id ? "Editar" : "Criar";
    },
  },

  async created() {
    await this.getItems();
    await this.getCategory();
  },

  methods: {
    resetProduto() {
      this.produto = {
        id: null,
        name: null,
        price: null,
        image: null,
        description: null,
        idCategory: null,
      };
      this.ativo = false;
    },

    async deletarItem(items) {
      try {
        if (confirm(`Deseja deletar o items com id: ${items.id}.`)) {
          const response = await this.$api.post("/produto/destroy", {
            id: items.id,
          });
          alert("Item Deletado!");
          this.getItems();
          if (response.type == "error") {
            alert(response.message);
          }
        }
      } catch (error) {
        alert("Não foi possivel deletar!");
      }
    },

    async persist() {
      if (this.produto.id) {
        const response = await this.$api.post(
          `/produto/persist/${this.produto.id}`,
          this.produto
        );
      } else {
        const response = await this.$api.post(
          "/produto/persist",
          this.produto
        );
      }
      this.resetProduto();
      await this.getItems();
    },

    editItem(items) {
      this.produto = {
        ...items,
      };
      this.ativo = true;
    },

    watch: {
      ativo(valor) {
        if (valor == false) {
          this.resetProduto();
        }
      },
    },

    mudaPagina() {
      this.$router.push({ path: "/variaveis" });
    },

    async getItems() {
      const response = await this.$api.get("/produto");
      this.items = response.data;
    },
    async getCategory() {
      const response = await this.$api.get("/categoria");
      this.categorias = response.data;
    },

  },
};
</script>
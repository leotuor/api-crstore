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
          <h1>{{ tituloDialog }} um Cupom</h1>
        </v-card-title>
        <v-card-text>
          <v-row>
            <v-col>
              <v-text-field
                v-model="cupom.code"
                variant="outlined"
                label="Código"
                placeholder="Código"
              />
              <v-autocomplete
                v-model="cupom.type"
                label="Tipo"
                placeholder="Tipo"
              />
              <v-text-field
                v-model="cupom.value"
                variant="outlined"
                label="Valor"
                placeholder="Valor"
              />
              
              <v-text-field
                v-model="cupom.uses"
                variant="outlined"
                label="Usos"
                placeholder="Usos"
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
      cupom: {
        code: null,
        type: null,
        value: null,
        uses: null,
      },
      items: [],
      headers: [
        {
          title: "Código",
          key: "code",
        },
        {
          title: "Tipo",
          key: "type",
        },
        {
          title: "Valor",
          key: "value",
        },
        {
          title: "Usos",
          key: "uses",
        },
      ],
    };
  },

  computed: {
    tituloDialog: function () {
      return this.cupom.id ? "Editar" : "Criar";
    },
  },

  async created() {
    await this.getItems();
  },

  methods: {
    resetCupom() {
      this.cupom = {
        code: null,
        type: null,
        uses: null,
      };
      this.ativo = false;
    },

    async deletarItem(items) {
      try {
        if (confirm(`Deseja deletar o items com id: ${items.id}.`)) {
          const response = await this.$api.post("/cupom/destroy", {
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
      if (this.cupom.id) {
        const response = await this.$api.post(
          `/cupom/persist/${this.cupom.id}`,
          this.cupom
        );
      } else {
        const response = await this.$api.post(
          "/cupom/persist",
          this.cupom
        );
      }
      this.resetCupom();
      await this.getItems();
    },

    editItem(items) {
      this.cupom = {
        ...items,
      };
      this.ativo = true;
    },

    watch: {
      ativo(valor) {
        if (valor == false) {
          this.resetCupom();
        }
      },
    },

    async getItems() {
      const response = await this.$api.get("/cupom");
      this.items = response.data;
    },
    

  },
};
</script>
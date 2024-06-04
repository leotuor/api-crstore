<!-- eslint-disable vue/valid-v-slot -->
<template>
  <v-container class="justify-center mt-5">
    <TabelaDados
      titulo="Tabela pedidos"
      :headers="headers"
      @edit-item="editItem"
      @deletar-item="deletarItem"
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
                v-model="pedido.status"
                variant="outlined"
                label="Status"
                placeholder="Status"
              />
              <v-text-field
                v-model="pedido.total"
                variant="outlined"
                label="Total"
                placeholder="Total"
              />
              <v-text-field
                v-model="pedido.totalDiscount"
                variant="outlined"
                label="Desconto"
                placeholder="Desconto"
              />
              <v-autocomplete
                v-model="pedido.idUserCustumer"
                :items="usuarios"
                items-title="username"
                items-value="id"
              />
              <v-autocomplete
                v-model="pedido.idUserDelivery"
                :items="usuarios"
                items-title="username"
                items-value="id"
              />
              <v-autocomplete
                v-model="pedido.idAddress"
                :items="enderecos"
                items-title="street"
                items-value="id"
              />
              <v-autocomplete
                v-model="pedido.idPayment"
                :items="pagamentos"
                items-title="name"
                items-value="id"
              />
              <v-autocomplete
                v-model="pedido.idCupom"
                :items="cupons"
                items-title="code"
                items-value="id"
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
      textoPedido: null,
      usuarios: [],
      entregas: [],
      enderecos: [],
      pagamentos: [],
      cupons: [],
      pedido: {
        status: null,
        total: null,
        totalDiscount: null,
        idUserCustumer: null,
        idAddress: null,
        idPayment: null,
        idCupom: null,
        completeAddress: null
      },
      items: [],
      headers: [
        {
          title: "Identificador",
          key: "id",
        },
        {
          title: "Procedimento",
          key: "status",
        },
        {
          title: "Desconto",
          key: "totalDiscount",
        },
        {
          title: "Total",
          key: "total",
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
      return this.pedido.id ? "Editar" : "Criar";
    },
  },

  watch: {
      ativo(valor) {
        if (valor == false) {
          this.resetPedido();
        }
      },
    },

  async created() {
    await this.getItems();
    await this.getUser();
    await this.getAddresses();
    await this.getPayment();
    await this.getCupom();
  },

  methods: {
    resetPedido() {
      this.pedido = {
        status: null,
      total: null,
      totalDiscount: null,
      idUserCustumer: null,
      idUserDelivery: null,
      idAddress: null,
      idPayment: null,
      idCupom: null,
      };
      this.ativo = false;
    },

    async deletarItem(items) {
      try {
        if (confirm(`Deseja deletar o items com id: ${items.id}.`)) {
          const response = await this.$api.post("/pedido/destroy", {
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
      if (this.pedido.id) {
        await this.$api.post(
          `/pedido/persist/${this.pedido.id}`,
          this.pedido
        );
      } else {
        await this.$api.post(
          "/pedido/persist",
          this.pedido
        );
      }
      this.resetPedido();
      await this.getItems();
    },

    editItem(items) {
      this.pedido = {
        ...items,
      };
      this.ativo = true;
    },

    async getItems() {
      const response = await this.$api.get("/pedido");
      this.items = response.data;
    },
    async getUser() {
      const response = await this.$api.get("/usuario");
      this.usuarios = response.data;
    },
    async getAddresses() {
      const response = await this.$api.get("/endereco");
      this.enderecos = response.data;
      // this.enderecos.foreach((endereco) => {
      //   endereco.completeAddress = `${endereco.street} ${endereco.numberForget} - ${endereco.district} ${endereco.state}`
      // });
    },
    async getPayment() {
      const response = await this.$api.get("/pagamento");
      this.pagamentos = response.data;
    },
    async getCupom() {
      const response = await this.$api.get("/cupom");
      this.cupons = response.data;
    },

  },
};
</script>
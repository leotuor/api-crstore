<template>
  <body style=" max-height: 100dvh; overflow-y: scroll;">
    <v-container grid-list-lg>
      
      <v-row>
        <v-col v-for="(produto, i) in items"
        :key="i"
        class="justify-center"
        max-width="350"
        md="4"
        >
        <v-card
        :title="produto.name"
        :subtitle="produto.description"
        @click=""
        
        >
        <v-container class="d-flex justify-center">
          <img :src="produto.image" alt="" style="height: 200px;">
        </v-container>
        </v-card>
      </v-col>
    </v-row>
    </v-container>
    
  </body>
</template>
<script>
export default{
  data () {
    return{
      items: [],
      produto: {
        id: null,
        name: null,
        price: null,
        image: null,
        description: null,
        idCategory: null,
      }
    }
  },
  async created() {
    await this.getItems();
  },
  methods: {
    async getItems() {
      const response = await this.$api.get("/produto");
      this.items = response.data;
    },
  }
}
</script>
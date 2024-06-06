<template>
  <v-card>
    <v-layout>
      <v-app-bar
        color="#860001"
        prominent
      >
      <v-app-bar-nav-icon variant="text" @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
    </v-app-bar>
    <v-navigation-drawer
      v-model="drawer"
      :location="$vuetify.display.mobile ? 'bottom' : undefined"
      temporary
      color="#710001"
    >
      <v-list>
        <v-list-item
          prepend-avatar="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTe0fHyMlckkgcn-A216gegzcerTbh9Zt2nrg&s"
          subtitle="User@email.com"
          title="Usuario"
        ></v-list-item>
      </v-list>

      <v-divider></v-divider>

      <v-list density="compact" nav>
        <v-list-item to="/" prepend-icon="mdi-home" title="Home" value="homepage"></v-list-item>
        <v-list-item to="/loja" prepend-icon="mdi-cart" title="Loja" value="lojinha"></v-list-item>
        <v-list-item to="/produtos" prepend-icon="mdi-file-edit" title="Cadastrar Produtos" value="produtos"></v-list-item>
        <v-list-item to="/cupom" prepend-icon="mdi-format-list-bulleted" title="Criar cupons" value="cupons"></v-list-item>
      </v-list>
    </v-navigation-drawer>
      <v-main style="height: 100dvh">
        <slot/>
      </v-main>
    </v-layout>
  </v-card>
</template>

<script>
export default{
  data: () => ({
    drawer: false,
  })
  
}
</script>
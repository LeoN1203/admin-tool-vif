<template>
  <v-footer class="bg-blue-grey-darken-4" max-height=10px>
    <v-row justify="center" no-gutters>
      <v-btn
        v-for="link in links"
        :key="link"
        class="mx-2"
        color="blue-grey-lighten-4"
        rounded="xl"
        variant="text"
      >
        {{ link }}
      </v-btn>
      <v-col class="text-center text-blue-grey-lighten-1 mt-4 font-weight-light" cols="12">
        &COPY;{{ new Date().getFullYear() }} — Admin Tool
      </v-col>
    </v-row>
  </v-footer>
</template>
<script>
  export default {
    data: () => ({
      links: [
        'Accueil',
        'A Propos',
        'Notre Équipe',
        'Services',
        'Blog',
        'Nous Contacter',
      ],
    }),
  }
</script>
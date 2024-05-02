<template>
  <v-data-table class="d-flex pa-6" :headers="headers" :items="categories">
    <template v-slot:top>
      <v-app-bar color="cyan-darken-4" prominent>
        <v-app-bar-nav-icon variant="text" @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
        <v-toolbar-title><strong class="font-weight-black">Dashboard</strong></v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>
        <v-dialog v-model="dialog" max-width="500px">
          <template v-slot:activator="{ props }">
            <v-btn class="mb-2" color="white" dark v-bind="props">
              <v-icon icon="mdi-plus-box" class="pa-3"></v-icon>
              Ajouter une cat&eacute;gorie
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>
            <v-card-text>
              <v-container>
                <v-col>
                  <v-row cols="6" md="8" sm="6">
                    <v-text-field v-model="editedItem.name" label="Titre"></v-text-field>
                  </v-row>
                  <v-row cols="6" md="8" sm="6">
                    <v-text-field v-model="editedItem.description" label="Description"></v-text-field>
                  </v-row>
                  <v-row cols="6" md="8" sm="6">
                    <v-text-field v-model="editedItem.status" label="État"></v-text-field>
                  </v-row>
                </v-col>
              </v-container>
            </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue-darken-5" variant="text" @click="close">
                Annuler
              </v-btn>
              <v-btn color="blue-darken-5" variant="text" @click="save">
                Ok
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5">&Ecirc;tes-vous sur d'effacer cet &eacute;l&eacute;ment ?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue-darken-5" variant="text" @click="closeDelete">Annuler</v-btn>
              <v-btn color="blue-darken-5" variant="text" @click="deleteItemConfirm">OK</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-app-bar>
      
      <v-navigation-drawer v-model="drawer" temporary>
        <v-list-item v-for="item in items" :value=item.value class="pa-6 justify-space-around" @click="initialize">
          <v-icon :icon=item.icon class="mr-3"></v-icon> {{ item.title }}
        </v-list-item>
      </v-navigation-drawer>
    </template>

    <template v-slot:item.actions="{ item }">
      <v-icon class="me-2" size="small" @click="editItem(item)">
        mdi-pencil
      </v-icon>
      <v-icon size="small" @click="deleteItem(item)">
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn color="primary" @click="initialize">
        Reset
      </v-btn>
    </template>
  </v-data-table>
</template>
<script>
export default {

  data: () => ({
    dialog: false,
    dialogDelete: false,
    headers: [
      {
        title: 'Titre',
        align: 'start',
        key: 'name',
      },
      { title: 'Description', key: 'description' },
      { title: 'État', key: 'status' },
      { title: 'Actions', key: 'actions', sortable: false },
    ],
    categories: [],
    editedIndex: -1,
    editedItem: {
      name: '',
      description: '',
      status: '',
    },
    defaultItem: {
      name: '',
      description: '',
      status: '',
    },
    drawer: false,
    group: null,
    items: [
      {
        title: 'Menu principal',
        value: 'menu-principal',
        icon: 'mdi-home',
      },
      {
        title: 'Projets',
        value: 'projets',
        icon: 'mdi-folder',
      },
      {
        title: 'Graphiques',
        value: 'graphiques',
        icon: 'mdi-chart-bar',
      },
      {
        title: 'Paramètres',
        value: 'paramètres',
        icon: 'mdi-cog',
      },
      {
        title: 'Profil',
        value: 'profil',
        icon: 'mdi-account-circle',
      }
    ],
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'Nouvelle catégorie' : 'Modifier la catégorie';
    },
  },

  watch: {
    dialog(val) {
      val || this.close()
    },
    dialogDelete(val) {
      val || this.closeDelete()
    },
    group() {
      this.drawer = false
    }
  },

  created() {
    this.initialize();
  },

  methods: {
    getCategories() {
      const data = window.localStorage.getItem('categories');
      if (data == null || data === '[]') {
        return new Object(
          [
            {
              name: 'Accueil',
              description: 'Page d\'acceuil',
              status: 'Active'
            },
            {
              name: 'Calendrier',
              description: 'Calendrier de l\'utilisateur',
              status: 'Active'
            },
            {
              name: 'Chat',
              description: 'Chat en ligne',
              status: 'En Maintenance'
            }
          ]);
      }
      return JSON.parse(data);
    },

    setCategories(categories) {
      window.localStorage.setItem('categories', JSON.stringify(categories));
      this.categories = categories;
    },

    initialize() {
      this.categories = this.getCategories();
      console.log(this.categories);
      window.localStorage.setItem('categories', JSON.stringify(this.categories));
    },

    editItem(item) {
      this.editedIndex = this.categories.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.dialog = true
    },

    deleteItem(item) {
      this.editedIndex = this.categories.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.dialogDelete = true
    },

    deleteItemConfirm() {
      this.categories.splice(this.editedIndex, 1)
      this.setCategories(this.categories);
      this.closeDelete()
    },

    close() {
      this.dialog = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    closeDelete() {
      this.dialogDelete = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.categories[this.editedIndex], this.editedItem)
      } else {
        this.categories.push(this.editedItem)
      }
      this.setCategories(this.categories);
      this.close()
    },
  },
}
</script>
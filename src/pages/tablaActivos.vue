<!-- eslint-disable vue/valid-v-slot -->
<template>
  <v-data-table
    :headers="headers"
    :items="activos"
    :sort-by="[{ key: 'id', order: 'asc' }]"
  >
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>Tabla Activos</v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>
        <v-dialog v-model="dialog" max-width="500px">
          <template v-slot:activator="{ props }">
            <v-btn class="mb-2" color="primary" dark v-bind="props">
              Nuevo Activo
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedItem.id"
                      label="ID Activo"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedItem.numeroSerie"
                      label="Numero de Serie"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedItem.numeroInventario"
                      label="Numero de Inventario"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedItem.tipo"
                      label="Tipo de Activo"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedItem.descripcion"
                      label="Descripcion del Activo"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedItem.ubicacion"
                      label="Ubicación"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedItem.responsable"
                      label="Responsable"
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue-darken-1" variant="text" @click="close">
                Cancelar
              </v-btn>
              <v-btn color="blue-darken-1" variant="text" @click="save">
                Guardar
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5">
              ¿Estás seguro de que deseas eliminar este elemento?
            </v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue-darken-1" variant="text" @click="closeDelete">
                Cancelar
              </v-btn>
              <v-btn color="blue-darken-1" variant="text" @click="deleteItemConfirm">
                OK
              </v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
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
        Reiniciar
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
        title: 'ID Activo',
        align: 'start',
        sortable: false,
        key: 'id',
      },
      { title: 'Numero de Serie', key: 'numeroSerie' },
      { title: 'Numero de Inventario', key: 'numeroInventario' },
      { title: 'Tipo de Activo', key: 'tipo' },
      { title: 'Descripcion', key: 'descripcion' },
      { title: 'Ubicación', key: 'ubicacion' },
      { title: 'Responsable', key: 'responsable' },
      { title: 'Acciones', key: 'actions', sortable: false },
    ],
    activos: [],
    editedIndex: -1,
    editedItem: {
      id: '',
      numeroSerie: '',
      numeroInventario: '',
      tipo: '',
      descripcion: '',
      ubicacion: '',
      responsable: '',
    },
    defaultItem: {
      id: '',
      numeroSerie: '',
      numeroInventario: '',
      tipo: '',
      descripcion: '',
      ubicacion: '',
      responsable: '',
    },
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'Nuevo Activo' : 'Editar Activo';
    },
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },

  created() {
    this.initialize();
  },

  methods: {
    initialize() {
      this.activos = [
        {
          id: '1111',
          numeroSerie: '12345',
          numeroInventario: '0',
          tipo: 'computadora',
          descripcion: 'Computadora de escritorio',
          ubicacion: 'Mexico',
          responsable: 'Juan',
          imagen: 'computadora.jpg',
        },
        {
          id: '1112',
          numeroSerie: '678910',
          numeroInventario: '1',
          tipo: 'mobiliario',
          descripcion: 'Mesa',
          ubicacion: 'Mexico',
          responsable: 'Luis',
          imagen: 'mesa.jpg',
        },
        {
          id: '1113',
          numeroSerie: '101112',
          numeroInventario: '3',
          tipo: 'equipo de electronica',
          descripcion: 'celular',
          ubicacion: 'USA',
          responsable: 'Oscar',
          imagen: 'celular.jpg',
        },
      ];
    },

    editItem(item) {
      this.editedIndex = this.activos.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },

    deleteItem(item) {
      this.editedIndex = this.activos.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
    },

    deleteItemConfirm() {
      this.activos.splice(this.editedIndex, 1);
      this.closeDelete();
    },

    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.activos[this.editedIndex], this.editedItem);
      } else {
        this.activos.push(this.editedItem);
      }
      this.close();
    },
  },
};
</script>

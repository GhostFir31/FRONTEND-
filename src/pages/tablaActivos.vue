<!-- eslint-disable vue/valid-v-slot -->
<template>
  <v-data-table
    :headers="headers"
    :items="activos"
    :sort-by="[{ key: 'idActivo', order: 'asc' }]"
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
                      v-model="editedItem.idActivo"
                      label="ID Activo"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedItem.numSerie"
                      label="Numero de Serie"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedItem.numInventario"
                      label="Numero de Inventario"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedItem.tipoActivo"
                      label="Tipo de Activo"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedItem.descripcionActivo"
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
import axios from 'axios';

export default {
  data() {
    return {
      dialog: false,
      dialogDelete: false,
      headers: [
        {
          title: 'ID Activo',
          align: 'start',
          sortable: false,
          key: 'idActivo',
        },
        { title: 'Numero de Serie', key: 'numSerie' },
        { title: 'Numero de Inventario', key: 'numInventario' },
        { title: 'Tipo de Activo', key: 'tipoActivo' },
        { title: 'Descripcion', key: 'descripcionActivo' },
        { title: 'Ubicación', key: 'ubicacion' },
        { title: 'Responsable', key: 'responsable' },
        { title: 'Acciones', key: 'actions', sortable: false },
      ],
      activos: [],
      editedIndex: -1,
      editedItem: {
        idActivo: '',
        numSerie: '',
        numInventario: '',
        tipoActivo: '',
        descripcionActivo: '',
        ubicacion: '',
        responsable: '',
      },
      defaultItem: {
        idActivo: '',
        numSerie: '',
        numInventario: '',
        tipoActivo: '',
        descripcionActivo: '',
        ubicacion: '',
        responsable: '',
      },
    };
  },
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
      this.obtenerActivos();
    },
    obtenerActivos() {
      axios.get('http://localhost:3001/activos')
        .then(response => {
          this.activos = response.data;
        })
        .catch(error => {
          console.error('Error al obtener activos:', error);
        });
    },
    async save() {
      if (this.editedIndex > -1) {
        // Update existing activo
        try {
          await axios.put(`http://localhost:3001/activos/${this.editedItem.idActivo}`, this.editedItem);
          this.close();
          this.initialize(); 
        } catch (error) {
          console.error('Error al actualizar activo:', error);
        }
      } else {
       
        try {
          await axios.post('http://localhost:3001/activos', this.editedItem);
          this.initialize(); 
          this.close();
        } catch (error) {
          console.error('Error al agregar activo:', error);
        }
      }
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
    async deleteItemConfirm() {
      try {
        await axios.delete(`http://localhost:3001/activos/${this.editedItem.idActivo}`);
        this.initialize(); 
        this.closeDelete();
      } catch (error) {
        console.error('Error al eliminar activo:', error);
      }
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
  },
};
</script>

<!-- eslint-disable vue/valid-v-slot -->
<template>
  <v-data-table
    :headers="ubicacionHeaders"
    :items="ubicaciones"
    :sort-by="[{ key: 'idUbicacion', order: 'asc' }]"
  >
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>Tabla de Ubicaciones</v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>
        <v-dialog v-model="ubicacionDialog" max-width="500px">
          <template v-slot:activator="{ props }">
            <v-btn class="mb-2" color="primary" dark v-bind="props">
              Nueva Ubicación
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ ubicacionFormTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedUbicacion.idUbicacion"
                      label="ID Ubicación"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedUbicacion.descripcionUbicacion"
                      label="Descripción"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedUbicacion.activosAsociados"
                      label="Activos Asociados"
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue-darken-1" variant="text" @click="closeUbicacionDialog">
                Cancelar
              </v-btn>
              <v-btn color="blue-darken-1" variant="text" @click="saveUbicacion">
                Guardar
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="ubicacionDialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5">
              ¿Estás seguro de que deseas eliminar esta ubicación?
            </v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue-darken-1" variant="text" @click="closeUbicacionDeleteDialog">
                Cancelar
              </v-btn>
              <v-btn color="blue-darken-1" variant="text" @click="deleteUbicacionConfirm">
                OK
              </v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.actions="{ item }">
      <v-icon class="me-2" size="small" @click="editUbicacion(item)">
        mdi-pencil
      </v-icon>
      <v-icon size="small" @click="deleteUbicacion(item)">
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn color="primary" @click="initializeUbicaciones">
        Reiniciar
      </v-btn>
    </template>
  </v-data-table>
</template>

<script>
import axios from 'axios';

export default {
  data: () => ({
    ubicacionDialog: false,
    ubicacionDialogDelete: false,
    ubicacionHeaders: [
      { title: 'ID Ubicación', align: 'start', sortable: false, key: 'idUbicacion' },
      { title: 'Descripción', key: 'descripcionUbicacion' },
      { title: 'Activos Asociados', key: 'activosAsociados' },
      { title: 'Acciones', key: 'actions', sortable: false },
    ],
    ubicaciones: [],
    editedUbicacionIndex: -1,
    editedUbicacion: {
      idUbicacion: '',
      descripcionUbicacion: '',
      activosAsociados: '',
    },
    defaultUbicacion: {
      idUbicacion: '',
      descripcionUbicacion: '',
      activosAsociados: '',
    },
  }),

  computed: {
    ubicacionFormTitle() {
      return this.editedUbicacionIndex === -1 ? 'Nueva Ubicación' : 'Editar Ubicación';
    },
  },

  watch: {
    ubicacionDialog(val) {
      val || this.closeUbicacionDialog();
    },
    ubicacionDialogDelete(val) {
      val || this.closeUbicacionDeleteDialog();
    },
  },

  created() {
    this.initializeUbicaciones();
  },

  methods: {
    async initializeUbicaciones() {
      try {
        const response = await axios.get('http://localhost:3001/ubicaciones');
        this.ubicaciones = response.data;
      } catch (error) {
        console.error('Error al obtener ubicaciones:', error);
      }
    },

    async saveUbicacion() {
      if (this.editedUbicacionIndex > -1) {
        // Update existing ubicacion
        try {
          await axios.put(`http://localhost:3001/ubicaciones/${this.editedUbicacion.idUbicacion}`, this.editedUbicacion);
          this.closeUbicacionDialog();
          this.initializeUbicaciones(); // Refresh data after update
        } catch (error) {
          console.error('Error al actualizar ubicación:', error);
        }
      } else {
        // Add new ubicacion
        try {
          await axios.post('http://localhost:3001/ubicaciones', this.editedUbicacion);
          this.initializeUbicaciones(); // Refresh data after addition
          this.closeUbicacionDialog();
        } catch (error) {
          console.error('Error al agregar ubicación:', error);
        }
      }
    },

    editUbicacion(item) {
      this.editedUbicacionIndex = this.ubicaciones.indexOf(item);
      this.editedUbicacion = Object.assign({}, item);
      this.ubicacionDialog = true;
    },

    deleteUbicacion(item) {
      this.editedUbicacionIndex = this.ubicaciones.indexOf(item);
      this.editedUbicacion = Object.assign({}, item);
      this.ubicacionDialogDelete = true;
    },

    async deleteUbicacionConfirm() {
      try {
        await axios.delete(`http://localhost:3001/ubicaciones/${this.editedUbicacion.idUbicacion}`);
        this.initializeUbicaciones(); // Refresh data after deletion
        this.closeUbicacionDeleteDialog();
      } catch (error) {
        console.error('Error al eliminar ubicación:', error);
      }
    },

    closeUbicacionDialog() {
      this.ubicacionDialog = false;
      this.$nextTick(() => {
        this.editedUbicacion = Object.assign({}, this.defaultUbicacion);
        this.editedUbicacionIndex = -1;
      });
    },

    closeUbicacionDeleteDialog() {
      this.ubicacionDialogDelete = false;
      this.$nextTick(() => {
        this.editedUbicacion = Object.assign({}, this.defaultUbicacion);
        this.editedUbicacionIndex = -1;
      });
    },
  },
};
</script>

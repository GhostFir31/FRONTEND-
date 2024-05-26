<!-- eslint-disable vue/valid-v-slot -->
<template>
  <v-data-table
    :headers="responsableHeaders"
    :items="responsables"
    :sort-by="[{ key: 'idResponsable', order: 'asc' }]"
  >
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>Tabla de Responsables</v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>
        <v-dialog v-model="responsableDialog" max-width="500px">
          <template v-slot:activator="{ props }">
            <v-btn class="mb-2" color="primary" dark v-bind="props">
              Nuevo Responsable
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ responsableFormTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedResponsable.idResponsable"
                      label="ID Responsable"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedResponsable.numeroEmpleado"
                      label="Número de Empleado"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedResponsable.nombre"
                      label="Nombre"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      v-model="editedResponsable.activosCustodia"
                      label="Activos en Custodia"
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue-darken-1" variant="text" @click="closeResponsableDialog">
                Cancelar
              </v-btn>
              <v-btn color="blue-darken-1" variant="text" @click="saveResponsable">
                Guardar
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="responsableDialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5">
              ¿Estás seguro de que deseas eliminar este responsable?
            </v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue-darken-1" variant="text" @click="closeResponsableDeleteDialog">
                Cancelar
              </v-btn>
              <v-btn color="blue-darken-1" variant="text" @click="deleteResponsableConfirm">
                OK
              </v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.actions="{ item }">
      <v-icon class="me-2" size="small" @click="editResponsable(item)">
        mdi-pencil
      </v-icon>
      <v-icon size="small" @click="deleteResponsable(item)">
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn color="primary" @click="initializeResponsables">
        Reiniciar
      </v-btn>
    </template>
  </v-data-table>
</template>

<script>
export default {
  data: () => ({
    responsableDialog: false,
    responsableDialogDelete: false,
    responsableHeaders: [
      { title: 'ID Responsable', align: 'start', sortable: false, key: 'idResponsable' },
      { title: 'Número de Empleado', key: 'numeroEmpleado' },
      { title: 'Nombre', key: 'nombre' },
      { title: 'Activos en Custodia', key: 'activosCustodia' },
      { title: 'Acciones', key: 'actions', sortable: false },
    ],
    responsables: [],
    editedResponsableIndex: -1,
    editedResponsable: {
      idResponsable: '',
      numeroEmpleado: '',
      nombre: '',
      activosCustodia: '',
    },
    defaultResponsable: {
      idResponsable: '',
      numeroEmpleado: '',
      nombre: '',
      activosCustodia: '',
    },
  }),

  computed: {
    responsableFormTitle() {
      return this.editedResponsableIndex === -1 ? 'Nuevo Responsable' : 'Editar Responsable';
    },
  },

  watch: {
    responsableDialog(val) {
      val || this.closeResponsableDialog();
    },
    responsableDialogDelete(val) {
      val || this.closeResponsableDeleteDialog();
    },
  },

  created() {
    this.initializeResponsables();
  },

  methods: {
    initializeResponsables() {
      this.responsables = [
        {
          idResponsable: '00001',
          numeroEmpleado: '1',
          nombre: 'Juan',
          activosCustodia: 'computadora',
        },
        {
          idResponsable: '00002',
          numeroEmpleado: '2',
          nombre: 'Luis',
          activosCustodia: 'mesa',
        },
        {
          idResponsable: '00003',
          numeroEmpleado: '3',
          nombre: 'Oscar',
          activosCustodia: 'celular',
        },
      ];
    },

    editResponsable(item) {
      this.editedResponsableIndex = this.responsables.indexOf(item);
      this.editedResponsable = Object.assign({}, item);
      this.responsableDialog = true;
    },

    deleteResponsable(item) {
      this.editedResponsableIndex = this.responsables.indexOf(item);
      this.editedResponsable = Object.assign({}, item);
      this.responsableDialogDelete = true;
    },

    deleteResponsableConfirm() {
      this.responsables.splice(this.editedResponsableIndex, 1);
      this.closeResponsableDeleteDialog();
    },

    closeResponsableDialog() {
      this.responsableDialog = false;
      this.$nextTick(() => {
        this.editedResponsable = Object.assign({}, this.defaultResponsable);
        this.editedResponsableIndex = -1;
      });
    },

    closeResponsableDeleteDialog() {
      this.responsableDialogDelete = false;
      this.$nextTick(() => {
        this.editedResponsable = Object.assign({}, this.defaultResponsable);
        this.editedResponsableIndex = -1;
      });
    },

    saveResponsable() {
      if (this.editedResponsableIndex > -1) {
        Object.assign(this.responsables[this.editedResponsableIndex], this.editedResponsable);
      } else {
        this.responsables.push(this.editedResponsable);
      }
      this.closeResponsableDialog();
    },
  },
};
</script>

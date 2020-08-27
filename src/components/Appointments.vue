<template>
  <div>
    <v-container>
      <v-simple-table fixed-header height="300px">
        <thead>
          <tr>
            <th>Día</th>
            <th>Hora</th>
            <th>Nombre del paciente</th>
            <th>Motivo de consulta</th>
            <th></th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="appointment in appointments" :key="appointment.id">
            <td>{{ appointment.date }}</td>
            <td>{{ appointment.time }}</td>
            <td>{{ appointment.name }}</td>
            <td>{{ appointment.complain }}</td>
            <td>
              <v-tooltip top>
                <template v-slot:activator="{ on, attrs }">
                  <v-icon
                    v-if="appointment.name == user.displayName"
                    @click.prevent="(dialog = true), (id = appointment.id)"
                    v-on="on"
                    v-bind="attrs"
                  >mdi-calendar-remove</v-icon>
                </template>
                <span>Cancelar cita</span>
              </v-tooltip>
            </td>
          </tr>
        </tbody>
      </v-simple-table>
    </v-container>
    <v-row justify="center">
      <v-dialog v-model="dialog" persistent max-width="340">
        <v-card>
          <v-card-title class="headline">
            Estás a punto de eliminar esta cita médica
          </v-card-title>
          <v-card-text>¿Deseas continuar?</v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="teal darken-1" text @click="dialog = false">No</v-btn>
            <v-btn color="teal darken-1" text @click="Delete">Si</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-row>
    <v-snackbar v-model="snackbar" :timeout="timeout" top>
      Cita médica eliminada
      <template v-slot:action="{ attrs }">
        <v-btn icon v-bind="attrs" @click="snackbar = false">
          <v-icon>mdi-close</v-icon>
        </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script>
import { db } from "@/firebase";

export default {
  name: "Home",
  data() {
    return {
      id: null,
      dialog: false,
      snackbar: false,
      timeout: 2000,
    };
  },
  computed: {
    user() {
      return this.$store.state.user;
    },
  },
  methods: {
    Delete() {
      db.collection("appointments")
        .doc(this.id)
        .delete();
      this.dialog = false;
      this.snackbar = true;
      this.id = null;
    },
  },
  firestore() {
    return {
      appointments: db.collection("appointments").orderBy("date", "asc"),
    };
  },
};
</script>

<style>
.v-card__title {
  word-break: keep-all;
}
</style>

<template>
  <div>
    <v-container>
      <v-card class="px-10">
        <v-form @submit.prevent="addAppointment">
          <v-row>
            <v-col col lg="6" md="6" sm="12">
              <v-menu
                ref="menu1"
                v-model="menu1"
                :close-on-content-click="false"
                transition="scale-transition"
                offset-y
                max-width="290px"
                min-width="290px"
              >
                <template v-slot:activator="{ on, attrs }">
                  <v-text-field
                    v-model="dateFormatted"
                    @blur="date = parseDate(dateFormatted)"
                    label="Fecha"
                    clearable
                    prepend-icon="mdi-calendar"
                    v-bind="attrs"
                    v-on="on"
                    @click:clear="date = null"
                    required
                  ></v-text-field>
                </template>
                <v-date-picker
                  v-model="date"
                  no-title
                  locale="es-cl"
                  date-format="DD-MM-YYYY"
                  @change="menu1 = false"
                ></v-date-picker>
              </v-menu>
            </v-col>
            <v-col col lg="6" md="6" sm="12">
              <v-select
                :items="[
                  '8:00 AM',
                  '9:00 AM',
                  '10:00 AM',
                  '11:00 AM',
                  '12:00 AM',
                  '1:00 PM',
                ]"
                label="Hora"
                prepend-icon="mdi-clock-outline"
                v-model="hour"
                required
              ></v-select>
            </v-col>
          </v-row>
          <v-row>
            <v-col md="8" sm="12">
              <v-textarea
                v-model="text"
                label="Motivo de consulta"
                placeholder="Ej: Chequeo de rutina"
                prepend-icon="mdi-emoticon-sick-outline"
                rows="3"
                outlined
                clearable
                required
                auto-grow
                counter
              ></v-textarea>
            </v-col>
            <v-col md="4" sm="12">
              <v-btn
                class="ma-2"
                outlined
                color="indigo"
                type="submit"
                large
                block
                >Solicitar cita médica</v-btn>
            </v-col>
          </v-row>
        </v-form>
      </v-card>
    </v-container>
  </div>
</template>

<script>
import { db } from "@/firebase";
// import * as moment from "moment/moment";

export default {
  name: "NewAppointment",
  data: (vm) => ({
    date: new Date().toISOString().substr(0, 10),
    dateFormatted: vm.formatDate(new Date().toISOString().substr(0, 10)),
    menu1: false,
    hour: "8:00 AM",
    text: null,
  }),
  computed: {
    user() {
      return this.$store.state.user;
    },
    computedDateFormatted() {
      return this.formatDate(this.date);
    },
  },
  watch: {
    date(val) {
      this.dateFormatted = this.formatDate(this.date);
      console.log(val);
    },
  },
  methods: {
    logOut() {
      this.$store.dispatch("logOut");
    },
    addAppointment() {
      db.collection("appointments").add({
        date: this.date,
        time: this.hour,
        complain: this.text,
        name: this.user.displayName,
      });
      this.date = new Date().toISOString().substr(0, 10);
      this.hour = "8:00 AM";
      this.text = null;
      this.$router.push({ path: 'appointments' })
    },
    formatDate(date) {
      if (!date) return null;

      const [year, month, day] = date.split("-");
      return `${day}/${month}/${year}`;
    },
    parseDate(date) {
      if (!date) return null;

      const [day, month, year] = date.split("/");
      return `${year}-${month.padStart(2, "0")}-${day.padStart(2, "0")}`;
    },
  },
  firestore() {
    return {
      appointments: db.collection("appointments")
    };
  },
};
</script>

<style></style>

<template>
  <v-layout row wrap>
    <v-flex xs12 m6>
      <v-form ref="form" v-model="valid">
        <v-text-field v-model="name" :rules="[rules.required]" :counter="10" label="Name" required></v-text-field>
        <v-radio-group v-model="gender" row>
          <v-radio label="Male" value="male"></v-radio>
          <v-radio label="Female" value="female"></v-radio>
        </v-radio-group>
        <v-dialog
          ref="dialog"
          v-model="modal"
          :return-value.sync="date"
          persistent
          lazy
          full-width
          width="290px"
          hide-overlay
        >
          <v-text-field
            slot="activator"
            v-model="date"
            label="Birth Date"
            prepend-icon="event"
            :rules="[rules.required]"
            readonly
          ></v-text-field>
          <v-date-picker v-model="date" scrollable>
            <v-spacer></v-spacer>
            <v-btn flat color="primary" @click="modal = false">Cancel</v-btn>
            <v-btn flat color="primary" @click="$refs.dialog.save(date)">OK</v-btn>
          </v-date-picker>
        </v-dialog>
        <v-layout align-center justify-center fill-height row>
          <v-btn @click="generate">Generate My Japanese Name</v-btn>
        </v-layout>
      </v-form>
    </v-flex>
  </v-layout>
</template>
<script>
import axios from 'axios'
export default {
  data: function () {
    return {
      valid: true,
      name: null,
      gender: "male",
      date: null,
      modal: false,
      rules: {
        required: value => !!value || 'Required.'
      }
    }
  },
  methods: {
    generate: function(event) {

      if (this.$refs.form.validate()) {
        axios.get(process.env.baseUrl + '/api/names',{params: {name: this.name, sex: this.gender, birth_date: this.date}}).then( response => {
          this.$store.commit('name/set', response.data)
        }).catch(error => {
          console.log(error)
        })
      }
    }
  }
}
</script>

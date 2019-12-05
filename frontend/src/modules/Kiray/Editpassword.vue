<template>
  <div>
    <v-row justify="center">
      <v-dialog v-model="dialog" persistent max-width="600px">
        <!-- <template v-slot:activator="{ on }">
        <v-btn color="primary" dark  @click="goto('2020')">Batch 2020</v-btn>
        <v-btn color="primary" dark  @click="goto('2021')">Batch 2021</v-btn>
        <v-btn color="primary" dark  @click="goto('2022')">Batch 2022</v-btn>
        <v-btn color="primary" dark  @click="goto('Educator')">PN Educator</v-btn>
        </template>-->
        <v-card>
          <v-card-title>
            <span class="headline">Edit Password</span>
          </v-card-title>
          <v-card-text>
            <v-container>
              <v-row>
                <v-col cols="12">
                  <v-text-field
                    @click:append="showOldpass = !showOldpass"
                    :type="showOldpass ? 'text' : 'password'"
                    :append-icon="showOldpass ? 'mdi-eye' : 'mdi-eye-off'"
                    outlined
                    :rules="[rules.required, rules.min]"
                    label="Old Password"
                    v-model="oldpassword"
                    color="black"
                  />
                </v-col>

                <v-col cols="12">
                  <v-text-field
                    @click:append="showNewpass = !showNewpass"
                    :type="showNewpass ? 'text' : 'password'"
                    :append-icon="showNewpass ? 'mdi-eye' : 'mdi-eye-off'"
                    outlined
                    :rules="[rules.required, rules.min]"
                    label="New Password"
                    v-model="newpassword"
                    color="black"
                  />
                </v-col>

                <v-col cols="12">
                  <v-text-field
                    @click:append="showConpass = !showConpass"
                    :type="showConpass ? 'text' : 'password'"
                    :append-icon="showConpass ? 'mdi-eye' : 'mdi-eye-off'"
                    outlined
                    :rules="[rules.required, rules.min]"
                    label="Confirm new Password"
                    v-model="confirmpassword"
                    color="black"
                  />
                </v-col>
              </v-row>
            </v-container>
            <small>*indicates required field</small>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" text @click="dialog = false">Close</v-btn>
            <v-btn color="blue darken-1" text @click="save">Save</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-row>
    <v-card fluid width="70%">
      <v-card-title>
            <span class="headline">Edit Password</span>
          </v-card-title>
    <v-row>
      <v-col>
        <v-btn color="primary" x-large dark @click="goto('2020')">Batch 2020</v-btn>
      </v-col>
      <v-col>
        <v-btn color="primary" x-large dark @click="goto('2021')">Batch 2021</v-btn>
      </v-col>
      <v-col>
        <v-btn color="primary" x-large dark @click="goto('2022')">Batch 2022</v-btn>
      </v-col>
      <v-col>
        <v-btn color="primary" x-large dark @click="goto('educator')">PN Educator</v-btn>
      </v-col>
    </v-row>
    </v-card>

    <v-layout row justify-center>
      <v-dialog v-model="alert" max-width="300">
        <v-card>
          <v-card-title class="headline">Change Password Alert</v-card-title>
          <v-card-text>{{sms}}</v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="green darken-1" flat="flat" @click="alert = false">Goti it</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-layout>
  </div>
</template>
<script>
import axios from "axios";
/* eslint-disable */
export default {
  data: () => ({
    dialog: false,
    showOldpass: false,
    showNewpass: false,
    showConpass: false,
    oldpassword: "",
    newpassword: "",
    confirmpassword: "",
    dbpassword: null,
    sms: "",
    alert: false,
    rules: {
      required: value => !!value || "Required.",
      min: v => v.length >= 8 || "Min 8 characters"
    }
  }),
  methods: {
    goto(batch) {
      // let new_dataPass = {
      //   new_pass: this.newpassword
      // };
      axios.get("http://localhost:3232/getbatch/" + batch).then(resp => {
        this.dbpassword = resp.data.fake.password;
        this.batchId = resp.data.fake._id;
        if (this.dbpassword != null) {
          this.dialog = true;
        }
      });
    },
    save() {
      let new_data = {
        password: this.newpassword
      };
      if (this.dbpassword == this.oldpassword) {
        if (this.newpassword != this.confirmpassword) {
          this.sms = "New Password did not match";
          this.alert = true;
        } else {
          axios
            .put(`https://localhost:3232/updatepass/${this.batchId}`, new_data)
            .then(resp => {
              this.dialog = false;
            })
            .catch();
        }
      } else {
        this.sms = "Old Password is  incorrect";
        this.alert = true;
      }
    }
  }
};
</script>
<template> 
  <v-container>
        <template>
            <v-snackbar
                v-model="snackbar"
                :timeout="timeout"
                absolute
                bottom
                app
                right
                :color="snackcolor"
                >
                {{ snacktext }}
                <template v-slot:action="{ attrs }">
                    <v-btn 
                        color="#FFFFFF"
                        dark
                        vertical
                        text
                        v-bind="attrs"
                        @click="snackbar = false"
                    >
                        Cerrar
                    </v-btn>
                </template>
            </v-snackbar>
        </template>
        <v-layout
          justify-center
        >
          <v-card
            width=600
            tile
            flat
          >
            <v-col cols="12" sm="12" md="12">
              <v-img
                width=550
                src="../assets/logo.png"
                aspect-ratio="1.2"
                contain
              >
              </v-img>
            </v-col>
          </v-card>
          <v-dialog v-model="dialog" max-width="600px" persistent>
            <v-toolbar
              color="indigo"
              dark
            >
              <v-app-bar-nav-icon></v-app-bar-nav-icon>
              <v-toolbar-title>Cambio de Password Requerido</v-toolbar-title>
              <v-spacer></v-spacer>
              <v-btn icon>
                <v-icon>verified_user</v-icon>
              </v-btn>
            </v-toolbar>
              <v-card>
              <v-card-title>
                  <span class="headline"></span>
              </v-card-title>
              <v-card-text>
                  <v-container grid-list-md>
                      <v-row dense>
                          <v-col cols="12" sm="12" md="12">
                              <v-text-field type="password" v-model="actualpassword" label="Password Actual">
                              </v-text-field>
                          </v-col>
                          <v-col cols="12" sm="12" md="12">
                              <v-text-field type="password" v-model="password" label="Nueva Password" required>
                              </v-text-field>
                          </v-col>
                          <v-col cols="12" sm="12" md="12">
                              <v-text-field type="password" v-model="retypepassword" label="Confirmar Password" required>
                              </v-text-field>
                          </v-col>
                      </v-row>
                      <v-col cols="12" sm="12" md="12" v-show="valida">
                          <div class="red--text" v-for="v in validaMensaje" :key="v" v-text="v">
                          </div>
                      </v-col> 
                  </v-container>
              </v-card-text>
              <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue darken-1" text @click="salir">Cancelar</v-btn>
                  <v-btn color="blue darken-1" text @click="guardar">Guardar</v-btn>
              </v-card-actions>
              </v-card>
          </v-dialog>
      </v-layout>
  </v-container>
</template>

<script>
  import axios from 'axios'
  export default {

name: 'TwpWorld',

    data: () => {
      return {
        snackbar: false,
        snackcolor: '',
        snacktext: '',
        timeout: 4000,
        actualpassword: '',
        password: '',
        retypepassword: '',
        dialog: false,
        validaMensaje: [],
        valida: 0
      }
    },
    watch: {
      dialog (val) {
      val || this.closeForm()
        },
      },
      created () {
        this.inicializar();
      },
    methods:{
      
      inicializar(){
        let me=this;
        me.dialog=me.$store.state.usuario.pxch=="SI"?true:false;
        me.actualpassword="";
        me.password="";
        me.retypepassword="";
      },
      salir(){
        this.$store.dispatch("salir");
      },
      closeForm(){
        this.dialog=false;
      },
      guardar(){
        let me=this;
        if (this.validar()){
            return;
        }
        let header={"Authorization" : "Bearer " + this.$store.state.token};
        let configuracion= {headers : header};
        axios.put('api/Users/Pxch',{
            'Id':me.$store.state.usuario.idusuario,
            'oldpassword':me.actualpassword,
            'newpassword':me.password,
        },configuracion).then(() => {
            me.salir();
        }).catch(function(error){
            me.snacktext = 'Se detectó un error. Código: '+ error.response.status;
            me.snackbar = true;
            me.snackcolor = 'error'
            console.log(error);
        });
      },
      validar(){
        this.valida=0;
        this.validaMensaje=[];

        if (this.password !== this.retypepassword){
            this.validaMensaje.push("No hay coincidencia en la verificacacón de Passwords.");
        }
        if (this.password.length<6){
            this.validaMensaje.push("El password debe poseer al menos 6 caracteres alfanumericos y/o especiales.");
        }
        if (this.validaMensaje.length){
            this.valida=1;
        }
        return this.valida;

      },
    },
  }
</script>

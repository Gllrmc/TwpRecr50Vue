<template>
    <v-row align="start">
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
        <v-col cols="12" md="10" sm="12">
            <v-data-table
            dense
            :headers="headersusuarios"
            :items="usuarios"
            :searchu="searchu"
            class="elevation-1"
            no-data-text="Nada para mostrar"
            >
                <template v-slot:top>
                    <v-toolbar flat color="white">
                        <div class="ma-2">
                            <v-btn small @click="crearPDF()"><v-icon>mdi-printer</v-icon></v-btn>
                        </div>
                        <v-toolbar-title>Personas</v-toolbar-title>
                        <v-divider
                            class="mx-4"
                            inset
                            vertical
                        ></v-divider>
                        <v-spacer></v-spacer>
                        <v-text-field dense label="Búsqueda" outlined v-model="searchu" clearable append-icon="mdi-search" single-line hide-details></v-text-field>
                        <v-spacer></v-spacer>
                        <v-dialog v-model="dialog" max-width="600px">
                            <template v-slot:activator="{ on }">
                            <v-btn color="primary" dark class="mb-2" v-on="on">Nuevo</v-btn>
                            </template>
                            <v-card>
                            <v-card-title>
                                <span class="headline">{{ formTitle }}</span>
                            </v-card-title>
                            <v-card-text>
                                <v-container grid-list-md>
                                    <v-row dense>
                                        <v-col cols="12" sm="6" md="6">
                                            <v-text-field dense v-model="email" label="Email" counter="50">
                                            </v-text-field>
                                        </v-col>
                                        <v-col cols="12" sm="6" md="6">
                                            <v-text-field dense type="password" v-model="password" label="Password">
                                            </v-text-field>
                                        </v-col>
                                        <v-col cols="12" sm="6" md="6">
                                            <v-text-field dense v-model="nombre" label="Nombre" counter="128">
                                            </v-text-field>
                                        </v-col>
                                        <v-col cols="12" sm="6" md="6">
                                            <v-text-field dense v-model="apellido" label="Apellido" counter="128">
                                            </v-text-field>
                                        </v-col>
                                        <v-col cols="12" sm="6" md="6">
                                            <v-select dense v-model="rolid"
                                            :items="roles" label="Rol">
                                            </v-select>
                                        </v-col>
                                        <v-col cols="12" sm="6" md="6">
                                            <v-text-field dense v-model="telefono" label="Teléfono" counter="20">
                                            </v-text-field>
                                        </v-col>
                                        <v-col cols="12" sm="2" md="2">
                                            <v-text-field dense v-model="iniciales" v-mask="up3" label="Iniciales" counter="3"></v-text-field>
                                        </v-col>
                                        <v-col cols=12 sm=4 md=4>
                                            <v-select dense v-model="coltexto" :items="textos" label="Color texto"></v-select>
                                        </v-col>
                                        <v-col cols=12 sm=4 md=4>
                                            <v-select dense v-model="colfondo" :items="colores" label="Color fondo"></v-select>
                                        </v-col>
                                        <v-col cols="12" sm="2" md="2">
                                            <v-avatar class="ml-2" v-if="coltexto=='black'" :color="colfondo" size=40>
                                                <span style="color:black">{{ iniciales }}</span>
                                            </v-avatar>
                                            <v-avatar class="ml-2" v-else :color="colfondo" size=40>
                                                <span style="color:white">{{ iniciales }}</span>
                                            </v-avatar>
                                        </v-col>
                                        <v-col cols="12" sm="4" md="4">
                                            <v-switch dense v-model="reclutador"
                                            flat
                                            label="Reclutador?"
                                            ></v-switch>                                    
                                        </v-col>
                                        <v-col cols="12" sm="4" md="4">
                                            <v-switch dense v-model="aplicante"
                                            flat
                                            label="aplicante?"
                                            ></v-switch>                                    
                                        </v-col>
                                        <v-col cols="12" sm="4" md="4">
                                            <v-switch dense v-model="administrativo"
                                            flat
                                            label="administrativo?"
                                            ></v-switch>                                    
                                        </v-col>
                                        <v-col cols="12" sm="3" md="3">
                                            <v-layout column>
                                                <v-avatar class="ml-2" size=40>
                                                    <v-img :src="imageUrl" aspect-ratio="2" contain></v-img>
                                                </v-avatar>
                                                <input v-show="false" ref="inputUpload1" type="file" @change="onFilePicked" >
                                            </v-layout>
                                        </v-col>
                                        <v-col cols="12" sm="3" md="3">
                                            <v-btn class="mx-2" small fab color="primary" @click="$refs.inputUpload1.click()">
                                                <v-icon dense dark>
                                                    mdi-plus
                                                </v-icon>    
                                            </v-btn>
                                            <v-btn class="mx-2" small fab color="primary" @click="clearImagen">
                                                <v-icon dense dark>
                                                    mdi-delete
                                                </v-icon>    
                                            </v-btn>
                                        </v-col>
                                        <v-col cols=12 sm=2 md=2>
                                            <v-select dense v-model="lineaspagina" :items="lineaspaginas" label="# lineas en tablas"></v-select>
                                        </v-col>
                                        <v-col cols="12" sm="3" md="3">
                                            <v-switch  densev-model="pxch" class="mx-2" label="Cambio Password"></v-switch>
                                        </v-col>
                                        <v-col cols="12" sm="12" md="12" v-show="valida">
                                            <div class="red--text" v-for="v in validaMensaje" :key="v" v-text="v">
                                            </div>
                                        </v-col>
                                    </v-row>
                                </v-container>
                            </v-card-text>
                            <v-card-actions>
                                <v-spacer></v-spacer>
                                <v-btn color="blue darken-1" text @click="close">Cancelar</v-btn>
                                <v-btn color="blue darken-1" text @click="guardar">Guardar</v-btn>
                            </v-card-actions>
                            </v-card>
                        </v-dialog>
                        <v-dialog v-model="adModal" max-width="390">
                            <v-card>
                                <v-card-title class="headline" v-if="adAccion==1">¿Activar Miembro?</v-card-title>
                                <v-card-title class="headline" v-if="adAccion==2">Bloquear Miembro?</v-card-title>
                                <v-card-text>
                                    Estás a punto de 
                                    <span v-if="adAccion==1">Activar </span>
                                    <span v-if="adAccion==2">Bloquear </span>
                                    esta Persona: {{ adNombre }}
                                </v-card-text>
                                <v-card-actions>
                                    <v-spacer/>
                                    <v-btn small @click="activarDesactivarCerrar">Cancel
                                    <v-icon>mdi-cancel</v-icon>
                                    </v-btn>
                                    <v-btn small v-if="adAccion==1" @click="activar">Activar
                                    <v-icon>mdi-lock_open</v-icon>
                                    </v-btn>
                                    <v-btn small v-if="adAccion==2" @click="desactivar">Bloquear
                                    <v-icon>mdi-lock</v-icon>
                                    </v-btn>
                                </v-card-actions>
                            </v-card>
                        </v-dialog>
                        <v-dialog v-model="recordInfo" max-width="400">
                            <v-card>
                                <v-card-title class="headline">Informacion del item</v-card-title>
                                <v-card-text>
                                    <p><b>Datos creacion:</b></p>
                                    {{iduseralta}}<br>
                                    {{fecalta}}
                                    <p/>
                                    <p><b>Datos ultima modificacion':</b></p>
                                    {{iduserumod}}<br>
                                    {{fecumod}}<br>
                                </v-card-text>
                                <v-card-actions>
                                    <v-spacer/>
                                    <v-btn small @click="recordInfo=false">Salir
                                    <v-icon>mdi-cancel</v-icon>
                                    </v-btn>
                                </v-card-actions>
                            </v-card>
                        </v-dialog>
                    </v-toolbar>
                </template>
                <template v-slot:[`item.actions`]="{ item }">
                    <v-tooltip bottom>
                        <template v-slot:activator="{ on, attrs }">
                            <v-icon
                            class="mr-2"
                            v-bind="attrs"
                            v-on="on"
                            @click="editItem(item)"
                            >
                            mdi-pencil
                            </v-icon>
                        </template>
                        <span>Editar</span>
                    </v-tooltip>
                    <v-tooltip bottom>
                        <template v-slot:activator="{ on, attrs }">
                            <v-icon
                            class="mr-2"
                            v-bind="attrs"
                            v-on="on"
                            @click="deleteItem(item)"
                            >
                            mdi-delete
                            </v-icon>
                        </template>
                        <span>Borrar</span>
                    </v-tooltip>
                    <v-tooltip bottom>
                        <template v-slot:activator="{ on, attrs }">
                            <template v-if="item.activo">
                                <v-icon
                                class="mr-2"
                                v-bind="attrs"
                                v-on="on"
                                @click="activarDesactivarMostrar(2,item)"
                                >
                                mdi-eye-off
                                </v-icon>
                            </template>
                            <template v-else>
                                <v-icon
                                class="mr-2"
                                v-bind="attrs"
                                v-on="on"
                                @click="activarDesactivarMostrar(1,item)"
                                >
                                mdi-eye
                                </v-icon>
                            </template>
                        </template>
                        <span>Act/Blo</span>
                    </v-tooltip>
                    <v-tooltip bottom>
                        <template v-slot:activator="{ on, attrs }">
                            <v-icon
                            class="mr-2"
                            v-bind="attrs"
                            v-on="on"
                            @click="infoItem(item)"
                            small
                            >
                            mdi-information-outline
                            </v-icon>
                        </template>
                        <span>Info</span>
                    </v-tooltip>
                </template>
                <template v-slot:[`item.activo`]="{ item }">
                    <td>
                        <div v-if="item.activo">
                            <v-chip class="ma-2" color="primary" text-color="white">Activo</v-chip>
                        </div>
                        <div v-else>
                            <v-chip class="ma-2">Inactivo</v-chip>
                        </div>
                    </td>
                </template>
                <template v-slot:[`item.imgusuario`]="{ item }">
                    <td>
                        <div v-if="item.imgusuario">
                            <v-avatar size=40>
                                <v-img :src="item.imgusuario" aspect-ratio="2" contain></v-img>
                            </v-avatar>
                        </div>
                        <div v-else>
                            <v-avatar v-if="item.coltexto=='black'" :color="item.colfondo" size=40>
                                <span style="color:black">{{ item.iniciales }}</span>
                            </v-avatar>
                            <v-avatar v-else :color="item.colfondo" size=40>
                                <span style="color:white">{{ item.iniciales }}</span>
                            </v-avatar>
                        </div>
                    </td>
                </template>
                <template v-slot:[`item.administrativo`]="{ item }">
                    <td>
                        <div v-if="item.administrativo">
                            <v-chip class="ma-2" color="primary" text-color="white">Si</v-chip>
                        </div>
                        <div v-else>
                            <v-chip class="ma-2">No</v-chip>
                        </div>
                    </td>
                </template>
                <template v-slot:[`item.reclutador`]="{ item }">
                    <td>
                        <div v-if="item.reclutador">
                            <v-chip class="ma-2" color="primary" text-color="white">Si</v-chip>
                        </div>
                        <div v-else>
                            <v-chip class="ma-2">No</v-chip>
                        </div>
                    </td>
                </template>
                <template v-slot:[`item.aplicante`]="{ item }">
                    <td>
                        <div v-if="item.aplicante">
                            <v-chip class="ma-2" color="primary" text-color="white">Si</v-chip>
                        </div>
                        <div v-else>
                            <v-chip class="ma-2">No</v-chip>
                        </div>
                    </td>
                </template>
                <template v-slot:[`item.fecalta`]="{ item }">
                    <td>{{ item.fecalta.substr(0, 16) }}</td>
                </template>
                <template v-slot:[`item.fecumod`]="{ item }">
                    <td>{{ item.fecumod.substr(0, 16) }}</td>
                </template>
                <template v-slot:no-data>
                    <v-btn color="primary" @click="listar">Resetear</v-btn>
                </template>
            </v-data-table>
        </v-col>
    </v-row>
</template>
<script>
  import axios from 'axios'
  import jsPDF from 'jspdf'
  export default {
    data: () => ({
        rules: {
            counter: value => value.length >= 3 && value.length <= 50  || 'Min 3 Max 50 characters'
        },
        up3:'AAA',
        colores: [
            {value: '#F44336', text: 'Rojo'},
            {value: '#E91E63', text: 'Rosa'},
            {value: '#9C27B0', text: 'Violeta'},
            {value: '#673AB7', text: 'Violeta oscuro'},                    
            {value: '#3F51B5', text: 'Indigo'},
            {value: '#2196F3', text: 'Azul'},
            {value: '#03A9F4', text: 'Celeste'},
            {value: '#00BCD4', text: 'Cianico'},
            {value: '#009688', text: 'Turquesa'},
            {value: '#4CAF50', text: 'Verde'},
            {value: '#8BC34A', text: 'Verde claro'},                    
            {value: '#CDDC39', text: 'Lima'},
            {value: '#FFEB3B', text: 'Amarillo'},
            {value: '#FFC107', text: 'Ambar'},
            {value: '#FF9800', text: 'Naranja'},
            {value: '#FF5722', text: 'Naranja oscuro'},
            {value: '#795548', text: 'Marron'},
            {value: '#607D8B', text: 'Lívido'},
            {value: '#9E9E9E', text: 'Gris'},
            {value: '#000000', text: 'Negro'},
            {value: '#FFFFFF', text: 'Blanco'}
        ],
        textos: [
            {value: 'white', text: 'Blanco'},
            {value: 'black', text: 'Negro'},
        ],
        lineaspaginas: [
            {value: 5, text: "5"},
            {value: 10, text: "10"},
            {value: 15, text: "15"},
            {value: -1, text: "All"},
        ],
        snackbar: false,
        snackcolor: '',
        snacktext: '',
        timeout: 4000,
        recordInfo:0,
        usuarios: [],
        roles:[],
        workuserId:'',
        imageUrl:'',
        dialog: false,
        groupdialog: false,
        proydialog: false,
        searchg: '',
        searchu: '',
        searchp: '',
        editedIndex: -1,
        id: '',
        rolid:'',
        iniciales:'',
        nombre:'',
        apellido:'',
        telefono: '',
        email: '',
        reclutador:false,
        aplicante:false,
        administrativo:false,
        password:'',
        colfondo:'#000000',
        coltexto:'black',
        imgusuario:'',
        primerahora: 0,
        lineaspagina: 0,
        pxch:false,
        iduseralta:'',
        fecalta:'',
        iduserumod:'',
        fecumod:'',
        activo:false,
        addgroup: '',
        addproy: '',
        actPassword:false,
        passwordAnt:'',
        valida: 0,
        validaMensaje:[],
        adModal: 0,
        adAccion: 0,
        adNombre: '',
        adId: ''             
    }),

    computed: {
        headersusuarios(){
            return [
                { text: '[Opciones]', value: 'actions', align: 'center', sortable: false },
                { text: 'Avatar', value: 'imgusuario', align: 'center', sortable: false },
                { text: 'First Name', value: 'nombre', align: 'start', sortable: true },
                { text: 'Last Name', value: 'apellido', align: 'start', sortable: true },
                { text: 'Initials', value: 'iniciales', align: 'start', sortable: true },
                { text: 'Teléfono', value: 'telefono', align: 'start', sortable: true },
                { text: 'email', value: 'email', align: 'start', sortable: true },
                { text: 'Rol', value: 'rol', align: 'start', sortable: true },
                { text: 'Administrative?', value: 'administrativo', align: 'start', sortable: true },
                { text: 'Recruiter?', value: 'reclutador', align: 'start', sortable: true },
                { text: 'Applicant?', value: 'aplicante', align: 'start', sortable: true },
                { text: 'LinXPag', value: 'lineaspagina', align: 'start', sortable: true },
                { text: 'Estado', value: 'activo', align: 'center', sortable: true  },
            ]
        },
        headersgrupos(){
            return [
                { text: '#', value: 'selected', align: 'center', sortable: false },
                { text: 'Nombre grupo', value: 'nombre', align: 'start', sortable: true },
            ]
        },
        headersproyectos(){
            return [
                { text: '#', value: 'selected', align: 'center', sortable: false },
                { text: 'Proyecto', value: 'nombre', align: 'start', sortable: true },
                { text: 'Tarifa', value: 'tarifaproyectousuario', align: 'end', sortable: true },
                { text: 'Costo', value: 'costoproyectousuario', align: 'end', sortable: true },
                { text: 'Notas', value: 'notas', align: 'start', sortable: true },
            ]
        },
        formTitle () {
            return this.editedIndex === -1 ? 'Nuevo miembro' : 'Actualizar miembro'
        },
    },

    watch: {
        dialog (val) {
            val || this.close()
        },
    },

    created () {
        this.select();
        this.listar()
    },

    methods: {
        save () {
        },
        cancel () {
        },
        pickFile () {
            this.$refs.image.click ()
        },
        onFilePicked (e) {
            const files = e.target.files
            if (files[0] !== undefined) {
                this.imageName = files[0].name
                if (this.imageName.lastIndexOf('.') <= 0) {
                return
                }
                const fr = new FileReader ()
                fr.readAsDataURL(files[0])
                fr.addEventListener('load', () => {
                    this.imageUrl = fr.result
                    this.imageFile = files[0] // this is an image file that can be sent to server...
                    this.imgusuario = this.imageUrl;
                })
            } else {
                this.imageName = ''
                this.imageFile = ''
                this.imageUrl = ''
            }
        },
        clearImagen(){
            this.imageUrl = ''
            this.imgusuario = ''
        },
        crearPDF(){
            var columns = [
                    {title: "Iniciales", dataKey: "iniciales"},
                    {title: "Nombre", dataKey: "nombre"},
                    {title: "Apellido", dataKey: "apellido"},
                    {title: "Rol", dataKey: "rol"},
                    {title: "reclutador?", dataKey: "reclutador"},
                    {title: "aplicante?", dataKey: "aplicante"},
                    {title: "administrativo?", dataKey: "administrativo"},
                    {title: "Telefono", dataKey: "telefono"},
                    {title: "Activo", dataKey: "activo"}
            ];
            var rows = [];

            this.usuarios.map(function(x){
                    rows.push({iniciales:x.iniciales,nombre:x.nombre,apellido:x.apellido,rol:x.rol,reclutador,x:reclutador,aplicante,x:aplicante,administrativo,x:administrativo,telefono:x.telefono,activo:x.activo});
            });

            // Only pt supported (not mm or in)
            var doc = new jsPDF('l', 'pt');
            doc.autoTable(columns, rows, {
                margin: {top: 60},
                addPageContent: () => {
                    doc.text("Listado de Personas", 40, 30);
                }
            });
            doc.save('Personas.pdf');
        },
        listar(){
            let me=this;
            let header={"Authorization" : "Bearer " + this.$store.state.token};
            let configuracion= {headers : header};
            axios.get('api/Users/Listar',configuracion).then(function(response){
                //console.log(response);
                me.usuarios=response.data;
            }).catch(function(error){
                me.snacktext = 'Se detectó un error. Código: '+ error.response.status;
                me.snackcolor = "error";
                me.snackbar = true;
                console.log(error);
            });
        },
        select(){
            let me=this;
            var rolesArray=[];
            let header={"Authorization" : "Bearer " + this.$store.state.token};
            let configuracion= {headers : header};
            axios.get('api/Roles/Select',configuracion).then(function(response){
                rolesArray=response.data;
                rolesArray.map(function(x){
                    me.roles.push({text: x.code,value:x.id});
                });
            }).catch(function(error){
                me.snacktext = 'Se detectó un error. Código: '+ error.response.status;
                me.snackcolor = "error";
                me.snackbar = true;
                console.log(error);
            });
        },
        editItem (item) {
            this.id=item.id;
            this.rolid=item.rolid;
            this.apellido=item.apellido;
            this.nombre=item.nombre;
            this.iniciales=item.iniciales;
            this.telefono=item.telefono;
            this.email=item.email;
            this.reclutador=item.reclutador;
            this.aplicante=item.aplicante;
            this.administrativo=item.administrativo;
            this.password=item.password_hash;
            this.passwordAnt=item.password_hash;
            this.colfondo=item.colfondo;
            this.coltexto=item.coltexto;
            this.imgusuario=item.imgusuario;
            this.imageUrl=item.imgusuario;
            this.primerahora=item.primerahora;
            this.lineaspagina=item.lineaspagina;
            this.pxch=item.pxch;
            this.iduseralta=item.iduseralta;
            this.fecalta=item.fecalta;
            this.iduserumod=item.iduserumod;
            this.fecumod=item.fecumod;
            this.activo=item.activo;
            this.editedIndex = 1;
            this.groupdialog = false;
            this.proydialog = false;
            this.dialog = true
        },
        deleteItem (item) {
            var me=this;
            var resulta = confirm('Esta seguro de querer borrar el registro?');
            if (resulta) {
                let header={"Authorization" : "Bearer " + me.$store.state.token};
                let configuracion= {headers : header};
                axios.delete('api/Users/Eliminar/'+item.id,configuracion).then( () => {
                    me.snacktext = 'Eliminacion exitosa';
                    me.snackcolor = "success";
                    me.snackbar = true;
                    me.close();
                    me.listar();
                }).catch(function(error){
                    me.snacktext = 'Se detectó un error. Código: '+ error.response.status;
                    me.snackcolor = "error";
                    me.snackbar = true;
                    console.log(error);
                });
            }
        },
        infoItem(item){
            var index = this.usuarios.findIndex(x => x.iduseralta === item.iduseralta);
            this.iduseralta=this.usuarios[index].email + ' ' + this.usuarios[index].iniciales +' '+ this.usuarios[index].apellido + ', ' + this.usuarios[index].nombre;
            this.fecalta=item.fecalta;
            this.iduserumod=this.usuarios[index].email + ' ' + this.usuarios[index].iniciales +' '+ this.usuarios[index].apellido + ', ' + this.usuarios[index].nombre;
            this.fecumod=item.fecumod;
            this.recordInfo = true;
        },
        closeInfo(){
            this.iduseralta='';
            this.fecalta='';
            this.iduserumod='';
            this.fecumod='';
            this.recordInfo = false;
        },
        close () {
            this.dialog = false
            this.limpiar();
        },
        limpiar(){
                this.id="";
                this.rolid="";
                this.iniciales=null;
                this.apellido="";
                this.nombre="";
                this.telefono="";
                this.email="";
                this.reclutador="";
                this.aplicante="";
                this.administrativo="";
                this.password="";
                this.passwordAnt="";
                this.colfondo="#000000";
                this.coltexto="white";
                this.imgusario="";
                this.imageUrl="";
                this.primerahora=0;
                this.lineaspagina=0;
                this.pxch=false;
                this.iduseralta = "";
                this.fecalta = "";
                this.iduserumod = "";
                this.fecumod = "";
                this.activo = false;          
                this.actPassword = false;
                this.groupdialog = false;
                this.editedIndex=-1;
        },
        guardar () {
            if (this.validar()){
                return;
            }
            var date = new Date();                
            let header={"Authorization" : "Bearer " + this.$store.state.token};
            let configuracion= {headers : header};
            if (this.editedIndex > -1) {
                //Código para editar
                //Código para guardar
                let me=this;
                if (me.password!=me.passwordAnt){
                    me.actPassword=true;
                }
                axios.put('api/Users/Actualizar',{
                    'Id':me.id,
                    'rolid':me.rolid,
                    'iniciales':me.iniciales,
                    'nombre':me.nombre,
                    'apellido':me.apellido,
                    'telefono': me.telefono,
                    'email':me.email,
                    'reclutador':me.reclutador,
                    'aplicante':me.aplicante,
                    'administrativo':me.administrativo,
                    'password':me.password,
                    'act_password':me.actPassword,
                    'colfondo':me.colfondo,
                    'coltexto':me.coltexto,
                    'imgusuario':me.imgusuario,
                    'primerahora':me.primerahora,
                    'lineaspagina':me.lineaspagina,
                    'pxch':me.pxch,
                    'iduseralta': me.iduseralta,
                    'fecalta': me.fecalta,
                    'iduserumod': me.$store.state.usuario.idusuario,
                    'fecumod': new Date(date.getTime() - (date.getTimezoneOffset() * 60000)).toISOString()                                            
                },configuracion).then( () => {
                    me.buscarUserinfo();
                    me.snacktext = 'Modificacion exitosa';
                    me.snackcolor = "success";
                    me.snackbar = true;
                    me.close();
                    me.listar();
                    me.limpiar();
                }).catch(function(error){
                    me.snacktext = 'Se detectó un error. Código: '+ error.response.status;
                    me.snackcolor = "error";
                    me.snackbar = true;
                    console.log(error);
                });
            } else {
                //Código para guardar
                let me=this;
                axios.post('api/Users/Crear',{
                    'rolid':me.rolid,
                    'iniciales':me.iniciales,
                    'nombre':me.nombre,
                    'apellido':me.apellido,
                    'telefono': me.telefono,
                    'email':me.email,
                    'reservado':me.reservado,
                    'password':me.password,
                    'colfondo':me.colfondo,
                    'coltexto':me.coltexto,
                    'imgusuario':me.imgusuario,
                    'primerahora':me.primerahora,
                    'lineaspagina':me.lineaspagina,
                    'pxch':me.pxch,
                    'iduseralta': me.$store.state.usuario.idusuario                      
                },configuracion)
                .then(function(response){
                    //console.log(response);
                    me.buscarUserinfo();
                    me.snacktext = 'Creacion exitosa';
                    me.snackcolor = "success";
                    me.snackbar = true;
                    me.close();
                    me.listar();
                    me.limpiar();
                }).catch(function(error){
                    me.snacktext = 'Se detectó un error. Código: '+ error.response.status;
                    me.snackcolor = "error";
                    me.snackbar = true;
                    console.log(error);
                });
            }
        },
        buscarUserinfo(){
            let me=this;
            let header={"Authorization" : "Bearer " + me.$store.state.token};
            let configuracion= {headers : header};
            axios.get('api/Users/Traer/'+me.$store.state.usuario.idusuario,configuracion)
            .then(respuesta => {
                return respuesta.data
            })
            .then(data => {
                this.$store.dispatch("guardarUserinfo", data)
            })
            .catch(function(error){
                me.snacktext = 'Se detectó un error. Código: '+ error.response.status;
                me.snackcolor = 'error'
                me.snackbar = true;
                console.log(error);
            });
        },
        validar(){
            this.valida=0;
            this.validaMensaje=[];

            if (this.apellido.length<2 || this.apellido.length>128){
                this.validaMensaje.push("El apellido debe tener más de 2 caracteres y menos de 128 caracteres.");
            }
            if (this.nombre.length<2 || this.nombre.length>128){
                this.validaMensaje.push("El nombre debe tener más de 2 caracteres y menos de 128 caracteres.");
            }
            if (!this.rolid){
                this.validaMensaje.push("Seleccione un rol.");
            }
            if (!this.email){
                this.validaMensaje.push("Ingrese el email del usuario.");
            }
            if (!this.password){
                this.validaMensaje.push("Ingrese el password del usuario.");
            }
            if (this.iniciales.length<1 || this.iniciales.length>3){
                this.validaMensaje.push("Las iniciales deben tener al menos 1 caracter y no mas de 3 caracteres.");
            }
            if (!this.coltexto){
                this.validaMensaje.push("Seleccione un color de texto.");
            }
            if (!this.colfondo){
                this.validaMensaje.push("Seleccione un color de fondo.");
            }
            if (this.validaMensaje.length){
                this.valida=1;
            }
            return this.valida;
        },
        activarDesactivarMostrar(accion,item){
            this.adModal=1;
            this.adNombre=item.apellido + ', ' + item.nombre;
            this.adId=item.id;                
            if (accion==1){
                this.adAccion=1;
            }
            else if (accion==2){
                this.adAccion=2;
            }
            else{
                this.adModal=0;
            }
        },
        activarDesactivarCerrar(){
            this.adModal=0;
        },
        activar(){
            let me=this;
            let header={"Authorization" : "Bearer " + this.$store.state.token};
            let configuracion= {headers : header};
            axios.put('api/Users/Activar/'+this.adId,{},configuracion).then( () => {
                me.snacktext = 'Activacion exitosa';
                me.snackcolor = "success";
                me.snackbar = true;
                me.adModal=0;
                me.adAccion=0;
                me.adNombre="";
                me.adId="";
                me.listar();                       
            }).catch(function(error){
                me.snacktext = 'Se detectó un error. Código: '+ error.response.status;
                me.snackcolor = "error";
                me.snackbar = true;
                console.log(error);
            });
        },
        desactivar(){
            let me=this;
            let header={"Authorization" : "Bearer " + this.$store.state.token};
            let configuracion= {headers : header};

            axios.put('api/Users/Desactivar/'+this.adId,{},configuracion).then( () => {
                me.snacktext = 'Desactivacion exitosa';
                me.snackcolor = "success";
                me.snackbar = true;
                me.adModal=0;
                me.adAccion=0;
                me.adNombre="";
                me.adId="";
                me.listar();                       
            }).catch(function(error){
                me.snacktext = 'Se detectó un error. Código: '+ error.response.status;
                me.snackcolor = "error";
                me.snackbar = true;
                console.log(error);
            });
        },
    },
  }
</script>

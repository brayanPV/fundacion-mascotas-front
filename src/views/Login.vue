<template>
  <div class="container-fluid mt--5">
    <div class="row">
      <div class="col-xl-12 order-xl-1">
        <card shadow type="secondary">
          <b-carousel id="carousel-1"
                      v-model="slide"
                      :interval="3000"
                      controls
                      indicators
                      background="#ababab"
                      img-width="1024"
                      img-height="480"
                      style="text-shadow: 1px 1px 2px #333;"
                      @sliding-start="onSlideStart"
                      @sliding-end="onSlideEnd"
          >
            <!-- Slides with custom text -->
            <b-carousel-slide caption="Fundación Huellitas"
                              :img-src="fundacion.nombrepropietario"
                              v-for="(fundacion, index) in fundaciones"
                              :key="index">
            </b-carousel-slide>
          </b-carousel>
        </card>
      </div>
    </div>

    <b-row>
      <b-col cols="9">
        <b-card-group columns>
          <b-card
            v-for="(mascota, index) in mascotas"
            :key="index"
            :title="'Mi nombre es ' + mascota.nombreMascota + ', Adoptame!'"
            :img-src="mascota.foto_mascota_ruta"
            img-alt="Image"
            img-top
          >
            <b-button @click="mostrarMascota(mascota)" variant="primary">Ver Mascota</b-button>
          </b-card>
        </b-card-group>
      </b-col>

      <b-col>
        <div class="clearfix">
          <b-badge variant="primary">
            Espacio publicitario.
          </b-badge>
          <b-img v-bind="{ width: 250, class: 'mx-2' }"
                 :src="require('../assets/Banners/banerV1.jpeg')"
                 alt="Left image"
                 class="my-2">
          </b-img>
          <b-img v-bind="{ width: 250, class: 'mx-1' }"
                 :src="require('../assets/Banners/banerV2.jpeg')"
                 alt="Left image">
          </b-img>
          <b-img  v-bind="{ width: 250, class: 'mx-1' }"
                 :src="require('../assets/Banners/banerV3.jpeg')"
                 alt="Left image">
          </b-img>
        </div>
      </b-col>
    </b-row>
  </div>
</template>
<script>
import {mapState, mapMutations} from 'vuex'
import axios from 'axios'
  export default {
    name: 'Index',
    data() {
      return {
        model: {
          correo: undefined,
          password: undefined
        },
      slide: 0,
      sliding: null,
      mascotas: [],
      fundaciones: []
      }
    },
    computed: {
        ...mapState(['servidorSeguridad', 'usuarios', 'servidorAcceso', 'menu', 'servidor']),
        ...mapMutations(['iniciarSesion', 'consultarSesion']),
        validarcorreo () {
            if (this.model.correo === '') {
                return false
            }
            else if (this.model.correo === undefined) {
                return undefined
            }
            return true
        },
        validarPassword () {
            if (this.model.password === '') {
                return false
            }
            else if (this.model.password === undefined) {
                return undefined
            }
            return true
        }
    },
    methods: {
        validarUsuarioActivo (datos) {
            if (datos.menuExtended.estado !== 'ACTIVO') {
                this.$toast.error({
                    title: 'Login Fallido',
                    message: 'Este usuario esta inactivo para hacer login'
                })
                return false
            } else {
                return true
            }
        },
    onSlideStart() {
        this.sliding = true
      },
      onSlideEnd() {
        this.sliding = false
      },
      async apiMascotasrandom () {
        this.loader = true
        this.itemsMascota = []
        axios.get(this.servidor + 'MascotaController_ListAll_Random.php').then(response => {
          if (response.data.result) {
            this.$toast.error({
              title: 'Información',
              message: response.data.result
            })
          } else {
            this.mascotas = response.data
          } 
        }).catch(() => {})
        this.loader = false
      },
      mostrarMascota (item) {
          this.$router.push({
            name: 'verMascota',
            params: {
              mascota: item
            }
          })
      },
      async apiFundacionRandom () {
        this.loader = true
        this.itemsMascota = []
        axios.get(this.servidor + 'FundacionControllerRamdon.php').then(response => {
          if (response.data.result) {
            this.$toast.error({
              title: 'Información',
              message: response.data.result
            })
          } else {
            this.fundaciones = response.data
          }
        }).catch(() => {})
        this.loader = false
      }
    },
    async created () {
      // this.$store.commit('cerrarSesion')
      await this.apiMascotasrandom()
      await this.apiFundacionRandom()
    }
  }
</script>
<style>
</style>

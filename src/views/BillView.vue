<template>
  <div class="bill">
    <div class="w-full my-1 mr-1 px-6 py-4 flex flex-col bg-gray-200 dark:bg-white
		dark:text-gray-400 rounded-r-lg overflow-y-auto">
      <!-- Right side NavBar -->


      <div class="flex justify-between">
        <div>
          <label class="w-full text-left">Precio del kw/h</label>
          <input
            class="bg-gray-100 pt-1 px-2 rounded-t hover:bg-gray-300 active:bg-white focus:bg-white border-b border-indigo-900 w-32 outline-none"
            v-model="energyPrice" type="number" placeholder="Precio" required />
        </div>


        <div>
          <button
            class="inline-flex px-4 rounded font-medium py-2 bg-indigo-500 hover:bg-indigo-600 text-white text-left"
            aria-controls="feedback-modal" @click.stop="informeModalOpen = true">
            <span>Generar informe</span>
          </button>
        </div>
      </div>

      <span class="mt-4 text-gray-600 text-left">Costo de la luz</span>
      <span class="my-2 text-3xl font-semibold text-left">$ {{ bills.length > 0 ?
          bills.reduce((total, obj) => {
            return (total + (obj.price * energyPrice))
          }, 0).toFixed(2) :
          0
      }}</span>

      <button class="inline-flex px-4 rounded font-medium py-2 bg-indigo-500 hover:bg-indigo-600 text-white text-left"
        aria-controls="feedback-modal" @click.stop="addModalOpen = true">

        <svg class="h-8 w-8 fill-current " viewBox="0 0 24 24">
          <path d="M19 13h-6v6h-2v-6H5v-2h6V5h2v6h6v2z"></path>
        </svg>
        <span class="mt-1 ml-2">Agregar</span>
      </button>


      <div class="mt-12 flex items-center">
        <!-- Payments -->
        <span>Electrodomesticos</span>
        <button class="ml-2 focus:outline-none">
          <svg class="h-5 w-5 fill-current" viewBox="0 0 256 512">
            <path d="M224.3 273l-136 136c-9.4 9.4-24.6 9.4-33.9
						0l-22.6-22.6c-9.4-9.4-9.4-24.6
						0-33.9l96.4-96.4-96.4-96.4c-9.4-9.4-9.4-24.6 0-33.9L54.3
						103c9.4-9.4 24.6-9.4 33.9 0l136 136c9.5 9.4 9.5 24.6.1
						34z"></path>
          </svg>
        </button>
      </div>

      <ECard @click="pop(bill)" v-for="(bill, index) in bills" :key="`Bill-${index}`" :image="bill.image"
        :name="`${bill.name}`" :avgprice="bill.price * energyPrice" :power="bill.consumption" />
      <ECard v-if="bills.length < 1" image='https://icon-library.com/images/not-found-icon/not-found-icon-18.jpg'
        :name="'Agregue un electrodoméstico para comenzar.'" :avgprice="'0'" :power="'0'">
      </ECard>
    </div>

    <ModalBasic id="create-modal" :modalOpen="addModalOpen" @close-modal="addModalOpen = false"
      title="Agregar electrodoméstico">
      <!-- Modal content -->
      <div class="px-5 py-4">
        <div class="text-sm">
          <div class="font-medium text-slate-800 mb-3">Agrega toda la información necesaria del consumo por hora de un
            electrodoméstico 🙌</div>
        </div>
        <div class="space-y-3">
          <div class="flex flex-col justify-items-start items-start">
            <label class="block text-sm font-medium mb-1" for="name">Icono</label>
            <div class="grid gap-2 grid-cols-8">
              <div @click="selectImage(img)" v-for="(img, i) in images" :key="i"
                class="h-14 w-14 hover:border-2 rounded-sm border-indigo-900">
                <img class="h-full" :src='img'>
              </div>
            </div>
          </div>
          <div class="flex flex-col justify-items-start items-start">
            <label class="block text-sm font-medium mb-1" for="name">Nombre del electrodoméstico</label>
            <Input v-model="data.name" type="text" placeholder="Ingresa el nombre del electrodoméstico" required />
            <div class="text-xs text-red-500 italic mt-2">{{ errorsForm1["name"] }} </div>
          </div>
          <div class="flex flex-col justify-items-start items-start">
            <label class="block text-sm font-medium mb-1" for="name">Horas de consumo diario</label>
            <Input v-model="data.hours" type="number" placeholder="Ingreso de horas de consumo diario" required />
            <div class="text-xs text-red-500 italic mt-2">{{ errorsForm1["hours"] }} </div>
          </div>
          <div class="flex flex-col justify-items-start items-start">
            <label class="block text-sm font-medium mb-1" for="name">Consumo del electrodoméstico en (W/h)</label>
            <Input v-model="data.consumption" type="number"
              placeholder="Ingrese el consumo de electrodoméstico en (W/h)" required />
            <div class="text-xs text-red-500 italic mt-2">{{ errorsForm1["consumption"] }} </div>
          </div>
        </div>
        <!-- Modal footer -->
        <div class="px-5 py-4 border-t border-slate-200">
          <div class="flex flex-wrap justify-end space-x-2">
            <button class="btn-sm border-slate-200 hover:border-slate-300 text-slate-600"
              @click="addModalOpen = false">Cancelar</button>
            <button @click="sendForm1" class="btn-sm bg-indigo-500 hover:bg-indigo-600 text-white">Agregar</button>
          </div>
        </div>
      </div>
    </ModalBasic>
    <ModalBasic id="feedback-modal" :modalOpen="editModalOpen" @close-modal="editModalOpen = false" title="Resumen">
      <!-- Modal content -->
      <div class="px-5 py-4">
        <div class="text-sm">
          <div class="font-medium text-slate-800 mb-3">Edita el valor del consumo eléctrico mensual🙌</div>
        </div>
        <div class="space-y-3">
          <div class="flex flex-col justify-items-start items-start">
            <label class="block text-sm font-medium mb-1" for="name"></label>
            <Input type="text" v-model="energyPrice" placeholder="Ingrese costo mensual de energía" required />
            <div class="text-xs text-red-500 italic mt-2">{{ errorsForm2["energyPrice"] }} </div>
          </div>
        </div>
        <!-- Modal footer -->
        <div class="px-5 py-4 border-t border-slate-200">
          <div class="flex flex-wrap justify-end space-x-2">
            <button class="btn-sm border-slate-200 hover:border-slate-300 text-slate-600"
              @click="editModalOpen = false">Cancelar</button>
            <button @click="sendForm2" class="btn-sm bg-indigo-500 hover:bg-indigo-600 text-white">Editar</button>
          </div>
        </div>
      </div>
    </ModalBasic>
    <ModalBasic id="feedback-modal" :modalOpen="informeModalOpen" @close-modal="informeModalOpen = false"
      title="Consumo eléctrico">
      <!-- Modal content -->
      <div class="px-5 py-4">

        <div class=" w-full bg-gray-50">
          <h1 class='text-2xl font-bold text-center'>Informe de consumo eléctrico mensual</h1>
          <div class="flex w-full justify-center">
            <table class="table-fixed border border-gray-500 border-collapse   mt-3">
              <thead class="">
                <tr class="border-b-2 border-black">
                  <th class="w-4/12 bg-gray-300 text-left px-2">-</th>
                  <th class="w-4/12 bg-gray-300 text-left px-2">Articulo</th>
                  <th class="w-4/12 bg-gray-300 text-left px-2">Horas/mes</th>
                  <th class="w-4/12 bg-gray-300 text-left px-2">Consumo mensual</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-400">
                <tr v-for="b in bills">
                  <td class="text-left px-2">
                    <div class="h-4">
                      <img class="h-4" :src="b.image" alt="">
                    </div>
                  </td>
                  <td class="text-left px-2">{{ b.name }}</td>
                  <td class="px-2">{{ b.price }}</td>
                  <td class="px-2">{{ b.consumption }}</td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
        <h1 class='text-lg font-bold text-center'>Consumo total: $ {{ bills.length > 0 ?
            bills.reduce((total, obj) => {
              return (total + (obj.price * energyPrice))
            }, 0).toFixed(2) :
            0
        }}</h1>
      </div>
    </ModalBasic>
  </div>
</template>
<script>
import { ref } from 'vue';
import ModalBasic from '../components/ModalBasic'
import Input from '../components/Input'
import ECard from '../components/ElectrodomesticCard.vue'
export default {
  name: 'BillView',
  components: {
    ModalBasic,
    Input,
    ECard
  },
  data() {
    return {
      price: 0,
      bills: []
    }
  },
  setup() {
    const addModalOpen = ref(false)
    const editModalOpen = ref(false)
    const energyPrice = ref(0.096)
    const errorsForm1 = ref({})
    const errorsForm2 = ref({})
    const informeModalOpen = ref(false)
    const images = ref([
      require('../assets/images/batidora-batidora.png'),
      require('../assets/images/cafetera.png'),
      require('../assets/images/electrodomestico.png'),
      require('../assets/images/hierro.png'),
      require('../assets/images/horno-microondas.png'),
      require('../assets/images/mezclador.png'),
      require('../assets/images/tostadora.png'),
      require('../assets/images/ventilador.png'),

    ])
    const data = ref({ image: null, name: null, hours: null, consumption: null })
    return {
      informeModalOpen,
      addModalOpen,
      editModalOpen,
      energyPrice,
      errorsForm2,
      data,
      errorsForm1,
      images
    }
  },
  methods: {
    pop(i) {
      this.bills = this.bills.filter(item => item !== i)
    },
    selectImage(image) {
      this.data.image = image
    },
    isNumeric: function (n) {
      return !isNaN(parseFloat(n) && isFinite(n));
    },

    resetData: function () {
      this.data.name = ''
      this.data.hours = ''
      this.data.consumption = ''
      this.data.image = ''
    },
    sendForm1: function () {
      if (this.checkForm1()) {
        this.$data.bills.push({
          image: this.data.image,
          name: this.data.name,
          price: this.data.hours * 30 * (this.data.consumption / 1000),
          consumption: this.data.consumption
        });
        this.resetData();
        this.addModalOpen = false;
      }
    },
    sendForm2: function () {
      if (this.checkForm2()) {
        this.editModalOpen = false;
      }
    },
    checkForm1: function () {
      this.errorsForm1 = {};
      if (!this.data.name) {
        this.errorsForm1.name = 'El nombre del electrodoméstico es requerido';
        return false;
      }
      else if (!this.data.hours) {
        this.errorsForm1.hours = 'Las horas de consumo son requeridas';
        return false;
      }
      else if (!this.isNumeric(this.data.hours)) {
        this.errorsForm1.hours = 'Las horas de consumo deben ser un número';
        return false;
      }
      else if (!this.data.consumption) {
        this.errorsForm1.consumption = 'El consumo del electrodoméstico es requerido';
        return false;
      }
      else if (!this.isNumeric(this.data.consumption)) {
        this.errorsForm1.consumption = 'El consumo del electrodoméstico debe ser un número';
        return false;
      }
      else {
        return true;
      }
    },
    checkForm2: function () {

      this.errorsForm2 = {};
      if (!this.energyPrice) {
        this.errorsForm2.energyPrice = 'El costo de energía es requerido';
        return false;
      }
      if (!this.isNumeric(this.energyPrice)) {
        this.errorsForm2.energyPrice = 'El costo de energía debe ser numérico';
        return false;
      }
      if (this.energyPrice) {
        return true;
      }

    },

  }
}
</script>
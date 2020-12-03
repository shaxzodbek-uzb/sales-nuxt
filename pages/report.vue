<template>
  <!-- This example requires Tailwind CSS v2.0+ -->
  <div class="flex flex-col">
    <div class="grid grid-cols-12 gap-6">
      <div class="col-span-3 sm:col-span-3">
        <label class="w-full md:pr-2">
          <span class="text-gray-700">ОТ</span>
          <client-only
            ><date-picker
              v-model="date_from"
              class="form-input mt-1 block w-full"
              placeholder="MM/DD/YYYY"
              format="MM/dd/yyyy"
          /></client-only>
        </label>
      </div>

      <div class="col-span-3 sm:col-span-3">
        <label class="w-full md:pr-2">
          <span class="text-gray-700">До</span>
          <client-only
            ><date-picker
              v-model="date_to"
              class="form-input mt-1 block w-full"
              placeholder="MM/DD/YYYY"
              format="MM/dd/yyyy"
          /></client-only>
        </label>
      </div>
      <div class="col-span-3 sm:col-span-3 pt-1">
        <button
          class="inline-flex justify-center py-2 mt-6 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
          @click="applyFilter"
        >
          Применять
        </button>
      </div>
    </div>
    <div class="-my-2 overflow-x-auto sm:-mx-6 lg:-mx-8">
      <div class="py-2 align-middle inline-block min-w-full sm:px-6 lg:px-8">
        <div
          class="shadow overflow-hidden border-b border-gray-200 sm:rounded-lg"
        >
          <table class="min-w-full divide-y divide-gray-200">
            <thead>
              <tr>
                <th
                  scope="col"
                  class="px-6 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
                >
                  Дата
                </th>
                <th
                  scope="col"
                  class="px-6 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
                >
                  Товар
                </th>
                <th
                  scope="col"
                  class="px-6 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
                >
                  Выручка
                </th>
                <th
                  scope="col"
                  class="px-6 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
                >
                  Себестоимость
                </th>
                <th
                  scope="col"
                  class="px-6 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
                >
                  Прибыль
                </th>
              </tr>
            </thead>
            <tbody class="bg-white divide-y divide-gray-200">
              <tr v-for="(item, index) in transactions" :key="item.id">
                <td class="px-6 py-4 whitespace-nowrap">
                  {{ item.performed_at }}
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  {{ item.batches[0].product.name }}
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  {{ getSoldPrice(item.batches, index) }}
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  {{ getPrice(item.batches, index) }}
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  {{ sold_price[index] - price[index] }}
                </td>
              </tr>
              <tr>
                <td class="px-6 py-4 whitespace-nowrap">Итого</td>
                <td class="px-6 py-4 whitespace-nowrap"></td>
                <td class="px-6 py-4 whitespace-nowrap">
                  {{ overall_sold() }}
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  {{ overall_product() }}
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  {{ overall_sold() - overall_product() }}
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      date_from: new Date(),
      date_to: new Date(),
      transactions: [],
      sold_price: [],
      price: [0],
      benefit: 0,
    }
  },
  computed: {},
  mounted() {
    this.loadData()
  },
  methods: {
    getSoldPrice(batches, index) {
      let price = 0
      for (let index = 0; index < batches.length; index++) {
        const element = batches[index]
        price += element.pivot_price * element.pivot_quantity
      }
      return (this.sold_price[index] = price)
    },

    getPrice(batches, index) {
      let price = 0
      for (let index = 0; index < batches.length; index++) {
        const element = batches[index]
        price += element.price * element.pivot_quantity
      }
      return (this.price[index] = price)
    },
    overall_sold() {
      return this.sold_price.reduce((t, n) => t + n, 0)
    },
    overall_product() {
      return this.price.reduce((t, n) => t + n, 0)
    },
    applyFilter() {
      this.loadData()
    },
    loadData() {
      const me = this
      this.$axios
        .$get(
          `/transactions?only=outcome&date_from=${this.$moment(
            this.date_from
          ).format('L')}&date_to=${this.$moment(this.date_to).format('L')}`
        )
        .then((res) => {
          while (me.transactions.pop());
          me.transactions.push(...res.data.transactions)
        })
        .catch((e) => {
          this.$toast.error('Ошибка в запросе')
        })
    },
  },
}
</script>

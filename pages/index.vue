<template>
  <div>
    <div class="md:grid md:grid-cols-3 md:gap-6">
      <div class="md:col-span-1">
        <div class="px-4 sm:px-0">
          <h3 class="text-lg font-medium leading-6 text-gray-900">
            Продать товар
          </h3>
          <p class="mt-1 text-sm text-gray-600">
            Эта информация будет общедоступна, поэтому будьте внимательны при
            передаче.
          </p>
        </div>
      </div>
      <div class="mt-5 md:mt-0 md:col-span-2">
        <div class="shadow sm:rounded-md">
          <div class="px-4 py-5 bg-white space-y-6 sm:p-6">
            <label class="block">
              <span class="text-gray-700">Название продукта *</span>
              <select
                v-model="form.products[0].id"
                class="form-select mt-1 block w-full"
              >
                <option
                  v-for="product in products"
                  :key="product.id"
                  :value="product.id"
                >
                  {{ product.name }}
                </option>
              </select>
            </label>
            <div class="flex md:flex-row flex-col">
              <label class="md:w-1/4 w-full md:pr-2">
                <span class="text-gray-700">Цена продажи *</span>
                <input
                  v-model="form.products[0].price"
                  type="number"
                  class="form-input mt-1 block w-full"
                  placeholder="2000"
                />
              </label>
              <label class="md:w-1/4 w-full md:pr-2">
                <span class="text-gray-700">Количество *</span>
                <input
                  v-model="form.products[0].quantity"
                  type="number"
                  class="form-input mt-1 block w-full"
                  placeholder="0"
                />
              </label>
              <label class="md:w-1/4 w-full md:pr-2">
                <span class="text-gray-700">Дата продажи </span>
                <client-only
                  ><date-picker
                    v-model="form.products[0].date"
                    class="form-input mt-1 block w-full"
                    placeholder="MM/DD/YYYY"
                    format="MM/dd/yyyy"
                /></client-only>
              </label>
              <label class="md:w-1/4 w-full">
                <span class="text-gray-700">Номер партии товара</span>
                <input
                  v-model="form.products[0].code"
                  class="form-input mt-1 block w-full"
                  placeholder="#######"
                />
              </label>
            </div>
          </div>
          <div class="px-4 py-3 bg-gray-50 text-right sm:px-6">
            <button
              class="inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
              @click="submitForm"
            >
              Сохранить
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      products: [],
      form: {
        type: 'outcome',
        products: [
          {
            id: 0,
            price: 0,
            quantity: 0,
            date: new Date(),
            code: null,
          },
        ],
      },
    }
  },
  mounted() {
    const me = this
    this.$axios
      .$get(`/products`)
      .then((res) => {
        while (me.products.pop());
        me.products.push(...res.data.products)
      })
      .catch((e) => {
        this.$toast.error('Ошибка в запросе')
      })
  },
  methods: {
    submitForm() {
      this.$toast.show('Отправка запроса ...')
      //   this.form.products[0].date = this.form.products[0].date.toLocaleDateString(
      //     'en-US'
      //   )
      this.$axios
        .$post('/transactions', this.form)
        .then((res) => {
          this.$toast.show('Успешно завершено!')
        })
        .catch((e) => {
          this.$toast.error('Ошибка в запросе')
        })
    },
  },
}
</script>

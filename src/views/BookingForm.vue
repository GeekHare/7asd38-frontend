<template>
  <div class="booking-form">
    <div
        v-if="isOrderSubmitted"
        class="thank-you"
    >
      <h1>Спасибо за Ваш заказ!</h1>
      <p>Мы выслали подтверждение на почту.</p>
      <p>Ожидайте, с вами свяжится менеджер в течении 30 минут.</p>
      <router-link to="/">Вернуться на главную</router-link>
    </div>
    <div
        v-if="!isOrderSubmitted"
        class="order"
    >
      <h1>Бронирование</h1>
      <div class="product">
        <ProductCard
            v-bind:img-link="this.product.img"
            v-bind:title="this.product.title"
            v-bind:show-details="false"
        ></ProductCard>
      </div>
      <ValidationObserver v-slot="{ invalid }">
        <p
            v-if="invalid"
            class="meessage"
        >
          Заполните форму, чтобы забронировать.
        </p>
        <form @submit.prevent="onSubmit">
          <ValidationProvider
              rules="required"
              v-slot="{ errors }"
              tag="div"
          >
            <label for="name">Имя</label>
            <input
                id="name"
                v-model="form.name"
                type="text"
                name="name"
            >
            <span class="error">{{ errors[0] }}</span>
          </ValidationProvider>
          <ValidationProvider
              rules="required"
              v-slot="{ errors }"
              tag="div"
          >
            <label for="date">Дата заезда</label>
            <Datepicker
                id="date"
                v-model="form.date"
                v-bind:disabled-dates="disabledDates"
                v-bind:language="ru"
                name="date"
            ></Datepicker>
            <span class="error">{{ errors[0] }}</span>
          </ValidationProvider>
          <ValidationProvider
              rules="required|between:1,365"
              v-slot="{ errors }"
              tag="div"
          >
            <label for="days">Количество дней пребывания</label>
            <input
                id="days"
                v-model="form.days"
                type="number"
                name="days"
                min="1"
                max="365"
            >
            <span class="error">{{ errors[0] }}</span>
          </ValidationProvider>
          <ValidationProvider
              rules="required|email"
              v-slot="{ errors }"
              tag="div"
          >
            <label for="email">Email</label>
            <input
                id="email"
                v-model="form.email"
                type="email"
                name="email"
                rules="email"
            >
            <span class="error">{{ errors[0] }}</span>
          </ValidationProvider>
          <ValidationProvider
              rules="required"
              v-slot="{ errors }"
              tag="div"
          >
            <label for="phone">Телефон</label>
            <input
                id="phone"
                v-model="form.phone"
                type="text"
                name="phone"
            >
            <span class="error">{{ errors[0] }}</span>
          </ValidationProvider>
          <button type="submit" v-bind:disabled="invalid">Забронировать</button>
        </form>
      </ValidationObserver>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import ProductCard from '@/components/ProductCard.vue';
import Datepicker from 'vuejs-datepicker';
import Moment from 'moment';
import axios from 'axios';
import { ru } from 'vuejs-datepicker/dist/locale';
import { ValidationProvider, ValidationObserver, extend } from 'vee-validate';
import { required, email, between } from 'vee-validate/dist/rules';

extend('required', {
  ...required,
  message: 'Обязательно для заполнения'
});
extend('between', {
  ...between,
  message: 'Необходимо указать значени от 1 до 365'
});
extend('email', {
  ...email,
  message: 'Введите корректный email'
});

@Component({
  components: {
    ProductCard,
    ValidationProvider,
    ValidationObserver,
    Datepicker,
  }
})
export default class BookingForm extends Vue {
  isOrderSubmitted = false;
  ru = ru;
  errors = [];
  product = {
    title: "",
    img: "",
  };
  form = {
    name: '',
    date: '',
    days: 1,
    email: '',
    phone: ''
  };

  created() {
    this.$store.state.loader = true;
    axios.get(process.env.VUE_APP_API_URL.concat('/products/', this.$router.currentRoute.params.id))
        .then(response => {
          this.$store.state.loader = false;
          this.product.title = response.data.title;
          this.product.img = response.data.main_photo_link;
        })
        .catch(error => {
          console.error(error.response.data);
        });
  }

  get disabledDates() {
    const toDate = new Date();
    toDate.setDate(toDate.getDate()-1);

    const fromDate = new Date();
    fromDate.setDate(fromDate.getDate()+365);

    return {
      to: toDate,
      from: fromDate
    };
  }

  onSubmit() {
    const date = Moment(this.form.date).format('YYYY-MM-DD');
    const data = {
      "booking_date": date,
      "count_days": this.form.days,
      "name": this.form.name,
      "email": this.form.email,
      "phone": this.form.phone,
      "product_id": this.$router.currentRoute.params.id,
    };
    this.$store.state.loader = true;
    axios.post(process.env.VUE_APP_API_URL.concat('/orders'), data)
        .then(() => {
          this.$store.state.loader = false;
          this.isOrderSubmitted = true;
        })
        .catch(error => {
          console.error(error.response.data);
          this.$store.state.loader = false;
        })
  }
}

</script>
<style lang="scss">
input {
  box-sizing: border-box;
  margin-top: 5px;
  width: 100%;
  padding: 7px;
  border: 1px solid #cecece;
  border-radius: 3px;
  &[name="days"] {
    width: 50px;
  }
}
</style>
<style scoped lang="scss">
h1, p.meessage {
  margin: 20px 0;
  text-align: center;
}
p.meessage {
  margin-top: 20px;
  color: #ffa700;
}
.product {
  max-width: 600px;
  margin: auto;
}
.booking-form {
  max-width: 800px;
  margin: auto;
  form {
    max-width: 300px;
    margin: auto;
    span.error {
      font-size: 12px;
      display: block;
      width: 100%;
      text-align: left;
      color: red;
    }
    label {
      display: block;
      text-align: left;
      &:nth-child(1n) {
        margin-top: 15px;
      }
    }
    button {
      margin: 20px auto auto;
      display: block;
      border: 1px solid #cecece;
      border-radius: 5px;
      width: 50%;
      padding: 10px;
      background-color: #ffaa00;
      font-weight: bold;
      text-transform: uppercase;
      text-decoration: none;
      color: #482900;
      &:hover {
        background-color: #ffbf00;
        cursor: pointer;
      }
      &:disabled {
        cursor: not-allowed;
        background-color: #e0e0e0;
      }
    }
  }
}
</style>
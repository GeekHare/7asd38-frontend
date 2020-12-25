<template>
  <div class="product">
    <div
        v-bind:style="imgBackgroundStyle"
        class="img"
    ></div>
    <div class="title">{{ title }}</div>
    <div
        v-if="isDisplayDetails"
        class="description"
    >
      {{ description }}
    </div>
    <div
        v-if="isDisplayDetails"
        class="price"
    >
      {{ costPerDay }}
    </div>
    <router-link
        v-if="isDisplayDetails"
        v-bind:to="link"
        class="btn-booking"
    >
      Забронировать
    </router-link>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component
export default class ProductCard extends Vue {
  @Prop() private id!: string;
  @Prop() private title!: string;
  @Prop() private imgLink!: string;
  @Prop() private description!: string;
  @Prop() private price!: string;
  @Prop() private showDetails!: boolean;

  get isDisplayDetails() {
    return this.showDetails !== undefined ? this.showDetails : true;
  }

  get link() {
    return String('/booking/').concat(this.id ?? '');
  }

  get imgBackgroundStyle() {
    const link = this.imgLink ?? 'https://fakeimg.pl/450x300/';
    return "background-image: url('" + link + "')";
  }

  get costPerDay() {
    return this.price !== undefined ? this.price.toString().concat(' RUB / ночь') : '- / ночь';
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.product {
  border: 1px solid #cecece;
  padding-bottom: 30px;
  border-radius: 5px;
  text-align: center;
  .img {
    background-size: cover;
    background-position: center;
    background-repeat: no-repeat;
    height: 200px;
    width: auto;
  }
  .title {
    margin-top: 20px;
    padding: 0px 10px;
    font-size: 23px;
  }
  .description {
    margin-top: 20px;
    padding: 0px 10px;
    color: #2d2d2d;
  }
  .price {
    margin-top: 20px;
    padding: 0px 10px;
  }
  .btn-booking {
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
  }
}
</style>

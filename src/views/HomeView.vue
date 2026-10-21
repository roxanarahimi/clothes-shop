<template>
  <div class="home">
    <div class="position-relative">
      <img src="/img/banner.png" width="100%" alt="">
      <div class="position-absolute w-100 h-100 d-grid" style="top:0 ; right: 0">
       <div class=" align-self-end mx-auto ms-5  mb-5">
         <h3 class="">جشنواره محصولات پاییزی</h3>
         <button class="btn btn-sm btn-dark rounded rounded-pill px-3">برو به فروشگاه</button>
       </div>

      </div>
    </div>
    <div class="row justify-content-center p-3 p-xl-5 pb-0">
      <h3 class="fw-bolder mb-3">دسته بندی ها</h3>
      <div class="row col-xl-11   justify-content-center justify-content-xl-between">
        <div v-for="item in 6" class="col-4 col-md-3 col-lg-2 col-xl-2 p-xl-1 mb-3 mb-xl-0">
          <img class="rounded rounded-circle" src="/img/img.png" width="100%" alt="">
        </div>
      </div>
    </div>
    <div class="row justify-content-center p-2 p-md-3 p-xl-5 new">
      <h3 class="fw-bolder mb-3">محصولات جدید</h3>
      <div class="row col-xl-11 justify-content-between">
        <div v-for="(item,index) in 6" :key="index" :class="{'col-6 col-md-3 mb-3 mb-xl-0 px-1 px-md-2':index!==0, 'col-12 col-md-9 mb-3 mb-xl-0 px-1 px-md-2':index===0}"  >
          <product-card0 class="d-none d-md-block" v-if="index=== 0"/>
          <product-card class="d-md-none" v-if="index=== 0 && index !==5"/>
          <product-card v-if="index!== 0 && index !==5" />
          <product-card class="d-none d-md-block" v-if="index=== 5"/>
        </div>
        <div class="text-center">
          <button class="btn  btn-dark rounded rounded-pill px-5">بیشـــتر</button>
        </div>
      </div>
    </div>

    <div class="parallax"></div>

    <div class="row justify-content-center p-2 p-xl-5 off">
      <h3 class="fw-bolder mb-3">تخفیفیءء ها</h3>
      <div class="row col-xl-11 justify-content-between">
        <div v-for="item in 4" class="col-6 col-md-3 mb-3 mb-xl-0 px-1 px-md-2">
          <product-card />
        </div>
        <div class="text-center">
          <button class="btn  btn-dark rounded rounded-pill px-5">بیشـــتر</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src

import ProductCard from "@/components/ProductCard.vue";
import ProductCard0 from "@/components/ProductCard0.vue";

export default {
  name: 'HomeView',
  components: {ProductCard0, ProductCard}
}
</script>

<template>
  <div
    class="card container mx-auto bg-white max-w-sm h-card w-card rounded-lg shadow-lg"
  >
    <div class="container mx-auto p-6">
      <div class="flex flex-col items-center">
        <div
          class="flex items-center justify-center w-28 h-28 rounded-full bg-secondary-50"
        >
          <a href="#">
            <img class="w-24 h-24 rounded-full" :src="vendor.img" alt="pic" />
          </a>
        </div>
        <div class="mt-5">
          <h1
            class="font-monts font-semibold text-lg truncate tracking-tight text-secondary-500"
          >
            <a href="#">{{ vendor.title }}</a>
          </h1>
          <ul
            class="flex items-center justify-center font-monts font-normal text-xs mb-3"
          >
            <li>
              <a href="#">Catering</a>
            </li>
            <span class="mx-1">|</span>
            <li>
              <a href="#">{{ vendor.location }}, {{ vendor.country }}</a>
            </li>
          </ul>
          <div class="flex items-center justify-center">
            <fa-rating
              :active-color="'#FBFF4A'"
              :glyph="thumbsUp"
              :item-size="20"
              :show-rating="false"
              :read-only="true"
              :increment="0.01"
              v-model="rating"
            ></fa-rating>
          </div>
          <div class="mb-4 font-monts font-light text-xs">
            <a href="#">{{ vendor.review }} reviews</a>
          </div>
          <a href="#">
            <button
              class="font-monts font-medium text-sm w-16 h-8 rounded-md bg-secondary-500 hover:bg-secondary-600"
            >
              {{ vendor.price }}
            </button>
          </a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { FaRating } from "vue-rate-it";
import ThumbsUp from "vue-rate-it/glyphs/star";

export default {
  name: "vendor",
  components: {
    FaRating,
  },
  props: ["vendor"],
  created() {
    this.thumbsUp = ThumbsUp;
  },
  data() {
    return {
      thumbsUp: "",
    };
  },
  computed: {
    rating() {
      return this.vendor.review > 5
        ? Number(this.vendor.review) / 5 / 10
        : Number(this.vendor.review);
    },
  },
};
</script>

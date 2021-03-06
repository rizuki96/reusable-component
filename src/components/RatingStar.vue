<template>
  <div class="star-rating">
    <div v-for="(star, index) in stars" :key="index" class="star-container">
      <svg
        class="star-svg"
        :style="[
          { fill: `url(#gradient${star.raw})` },
          { width: styleStarWidth },
          { height: styleStarHeight }
        ]"
      >
        <polygon :points="getStarPoints" style="fill-rule:nonzero;" />
        <defs>
          <!--
            id has to be unique to each star fullness(dynamic offset) - it indicates fullness above
          -->
          <linearGradient :id="`gradient${star.raw}`">
            <stop
              id="stop1"
              :offset="star.percent"
              stop-opacity="1"
              :stop-color="getFullFillColor(star)"
            />
            <stop
              id="stop2"
              :offset="star.percent"
              stop-opacity="0"
              :stop-color="getFullFillColor(star)"
            />
            <stop
              id="stop3"
              :offset="star.percent"
              stop-opacity="1"
              :stop-color="styleEmptyStarColor"
            />
            <stop id="stop4" offset="100%" stop-opacity="1" :stop-color="styleEmptyStarColor" />
          </linearGradient>
        </defs>
      </svg>
    </div>
    <div v-if="isIndicatorActive" class="indicator">{{ rating }}</div>
  </div>
</template>

<script>
export default {
  name: "stars-rating",
  components: {},
  props: {
    rating: {
      type: Number,
      default: 4.3
    },
    starStyle: {
      type: Object
    },
    isIndicatorActive: {
      type: Boolean,
      default: true
    }
  },
  data: function() {
    return {
      stars: [],
      emptyStar: 0,
      fullStar: 1,
      totalStars: 5,
      // Binded Nested Props registered as data/computed and not props
      styleStarWidth: 20,
      styleStarHeight: 20,
      styleEmptyStarColor: "gray",
      styleFullStarColor: "goldenrod"
    };
  },
  directives: {},
  computed: {
    getStarPoints: function() {
      let centerX = this.styleStarWidth / 2;
      let centerY = this.styleStarWidth / 2;

      let innerCircleArms = 5; // a 5 arms star

      let innerRadius = this.styleStarWidth / innerCircleArms;
      let innerOuterRadiusRatio = 1.8; // Unique value - determines fatness/sharpness of star
      let outerRadius = innerRadius * innerOuterRadiusRatio;

      return this.calcStarPoints(
        centerX,
        centerY,
        innerCircleArms,
        innerRadius,
        outerRadius
      );
    }
  },
  methods: {
    calcStarPoints(
      centerX,
      centerY,
      innerCircleArms,
      innerRadius,
      outerRadius
    ) {
      let angle = Math.PI / innerCircleArms;
      let angleOffsetToCenterStar = 60;

      let totalArms = innerCircleArms * 2;
      let points = "";
      for (let i = 0; i < totalArms; i++) {
        let isEvenIndex = i % 2 == 0;
        let r = isEvenIndex ? outerRadius : innerRadius;
        let currX = centerX + Math.cos(i * angle + angleOffsetToCenterStar) * r;
        let currY = centerY + Math.sin(i * angle + angleOffsetToCenterStar) * r;
        points += currX + "," + currY + " ";
      }
      return points;
    },
    initStars() {
      for (let i = 0; i < this.totalStars; i++) {
        this.stars.push({
          raw: this.emptyStar,
          percent: this.emptyStar + "%"
        });
      }
    },
    setStars() {
      let fullStarsCounter = Math.floor(this.rating);
      for (let i = 0; i < this.stars.length; i++) {
        if (fullStarsCounter !== 0) {
          this.stars[i].raw = this.fullStar;
          this.stars[i].percent = this.calcStarFullness(this.stars[i]);
          fullStarsCounter--;
        } else {
          let surplus = Math.round((this.rating % 1) * 10) / 10; // Support just one decimal
          let roundedOneDecimalPoint = Math.round(surplus * 10) / 10;
          this.stars[i].raw = roundedOneDecimalPoint;
          return (this.stars[i].percent = this.calcStarFullness(this.stars[i]));
        }
      }
    },
    getFullFillColor(starData) {
      return starData.raw !== this.emptyStar
        ? this.styleFullStarColor
        : this.styleEmptyStarColor;
    },
    calcStarFullness(starData) {
      let starFullnessPercent = starData.raw * 100 + "%";
      return starFullnessPercent;
    },
    setNestedConfigStyles(objToFlatten) {
      if (typeof objToFlatten === "object") {
        for (let i in objToFlatten) {
          let newKey =
            "style" + i.charAt(0).toUpperCase() + i.substring(1, i.length);
          this[newKey] = objToFlatten[i];
        }
      }
    }
  },
  created() {
    this.setNestedConfigStyles(this.starStyle);
    this.initStars();
    this.setStars();
  }
};
</script>

<style scoped lang="scss">
.star-rating {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  .star-container {
    display: flex;
    align-items: center;
    justify-content: center;
    .star-svg {
      display: block;
      height: 30px;
      width: 30px;
    }
  }
  .indicator {
  }
  .star-container:not(:last-child) {
    margin-right: 0em;
  }
}
</style>

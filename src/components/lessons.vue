<template>
  <div v-if="lessonsHasLessons">
    <div class="grid grid-cols-3 gap-4">
      <div
        v-for="(l, index) in lessons"
        :key="index"
        class="inline-flex w-full border rounded-lg p-3"
      >
        <img v-bind:src="l.image" alt="subject" class="h-48 w-48" />
        <div class="p-2">
          <p class="p-1 text-center">
            <strong>{{ l.subject }}</strong>
          </p>
          <p class="p-1">
            <strong>Location: </strong>
            {{ l.location }}
          </p>
          <p class="p-1">
            <strong>Price: </strong>
            ${{ l.price }}
          </p>
          <p class="p-1">
            <strong>Spaces: </strong>
            {{ l.spaces }}
          </p>
          <br />
          <button
            v-if="canAddToCart(l)"
            class="w-full rounded-md p-2 text-sm font-roboto bg-blue-600 text-white hover:bg-blue-700"
            @click="addProduct(l)"
          >
            Add to Cart
          </button>
          <button
            v-else
            disabled
            class="w-full rounded-md p-2 text-sm font-roboto bg-blue-500 text-white"
          >
            Add to Cart
          </button>
        </div>
      </div>
    </div>
  </div>
  <div v-else>No Lessons Found</div>
</template>

<script>
export default {
  name: "LessonsChild",
  props: {
    lessons: {
      type: Object,
    },
  },
  methods: {
    addProduct(product) {
      this.$emit("addProduct", product);
    },
    canAddToCart(lesson) {
      if (lesson.spaces > 0) {
        return true;
      } else {
        return false;
      }
    },
  },
  computed: {
    lessonsHasLessons() {
      if (this.lessons.length > 0 || this.lessons != undefined) {
        return true;
      } else {
        return false;
      }
    },
  },
};
</script>

<template>
  <div
    id="sidebar"
    class="fixed z-50 w-80 right-0 top-0 h-screen bg-gray-50 p-5 overflow-auto shadow-md transition-all transform translate-x-96 duration-500 opacity-0 invisible"
  >
    <div class="inline-flex w-full p-3">
      <h1 class="font-roboto font-bold text-xl">Cart</h1>
      <button type="button" v-on:click="hideSidebar" class="ml-auto font-bold">
        X
      </button>
    </div>
    <hr />
    <br />
    <div v-if="cartHasItems" class="w-full">
      <!-- Cart -->
      <div
        v-for="(c, index) in cart"
        v-bind:key="index"
        class="inline-flex w-full py-2 pb-5"
      >
        <img v-bind:src="c.image" class="w-28 h-28 rounded-md" alt="" />
        <div class="pl-4 py-2 w-full">
          <p><strong>Subject:</strong> {{ c.subject }}</p>
          <p class="pb-3"><strong>Quantity:</strong> {{ c.quantity }}</p>
          <button
            @click="removeFromCart(index)"
            class="bg-red-500 w-full rounded-md text-white p-1"
          >
            <i class="fas fa-trash-alt"></i>
          </button>
        </div>
      </div>

      <!-- Checkout -->
      <div class="p-3">
        <label for="">Name</label>
        <input
          type="text"
          v-model="name"
          class="p-2 w-full border rounded-md"
        />
        <label for="">Phone Number</label>
        <input
          type="number"
          v-model="number"
          class="p-2 w-full border rounded-md"
        />
        <br />
        <br />
        <button
          v-if="dataValidated"
          class="bg-blue-500 hover:bg-blue-600 rounded-md text-white w-full p-2"
          @click="processCheckout(name, number)"
        >
          Proceed to Checkout
        </button>
        <button
          v-else
          disabled
          class="bg-blue-400 rounded-md text-white w-full p-2"
        >
          Proceed to Checkout
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "CheckoutComponent",
  props: {
    cart: {
      type: Object,
    },
  },
  data() {
    return {
      dataValidated: false,
      nameValidated: false,
      numberValidated: false,
      name: "",
      number: "",
    };
  },
  methods: {
    hideSidebar: function () {
      const sidebar = document.getElementById("sidebar");
      sidebar.style.visibility = "invisible";
      sidebar.style.opacity = 0;
      sidebar.style.transform = "translateX(20rem)";
    },
    isDataValidated() {
      console.log("fired");
      if (this.numberValidated && this.nameValidated) {
        this.dataValidated = true;
        console.log("data validated");
      } else {
        this.dataValidated = false;
        console.log("data not validated");
        console.log(this.numberValidated);
        console.log(this.nameValidated);
      }
    },
    removeFromCart(index) {
      this.$emit("removeFromCart", index);
    },
    processCheckout(name, number) {
      this.$emit("processCheckout", { name, number });
    },
  },
  watch: {
    number: function (val) {
      var phoneno = /^\d{10}$/;
      if (String(val).match(phoneno)) {
        this.numberValidated = true;
      } else {
        this.numberValidated = false;
      }
      this.isDataValidated();
    },
    name: function (val) {
      console.log("watching Name");
      let number = /\d+/g;
      if (val.match(number)) {
        this.nameValidated = false;
        console.log("found number");
      } else {
        this.nameValidated = true;
        console.log("not found number");
      }
      this.isDataValidated();
    },
  },
  computed: {
    cartHasItems() {
      if (this.cart.length > 0) {
        return true;
      } else {
        return false;
      }
    },
  },
};
</script>

<template>
  <div class="w-screen min-h-screen bg-gray-200">
    <!-- Sidebar -->
    <checkout-component
      :cart="cart"
      @processCheckout="onSubmitCheckout"
      @removeFromCart="deleteItemFromCart"
    ></checkout-component>

    <!-- Navbar -->
    <div
      class="fixed z-40 inline-flex w-full shadow-md p-3 px-40 bg-white text-gray-900 font-serif text-lg"
    >
      <h1 class="font-roboto text-primary font-semibold text-xl">Lessons</h1>
      <button
        v-if="cartHasItems"
        v-on:click="showSidebar"
        class="ml-auto text-gray-700 hover:text-gray-900 font-sans"
      >
        <i class="fas fa-shopping-cart"></i>
        Cart
      </button>
      <button v-else class="ml-auto text-gray-500 font-sans" disabled>
        <i class="fas fa-shopping-cart"></i>
        Cart
      </button>
    </div>

    <div class="w-full grid grid-cols-2 gap-10 p-20 pt-32">
      <div class="inline-flex">
        <img src="bg.jpeg" alt="" class="w-full h-auto rounded-lg" />
      </div>
      <div class="w-full font-roboto py-10">
        <h1 class="text-left font-bold text-4xl max-w-sm">
          Find All Your Lessons In One Place
        </h1>
        <br />
        <p class="max-w-md">
          Lorem ipsum dolor sit amet consectetur adipisicing elit. Aspernatur
          perspiciatis animi voluptas corporis, numquam facilis id veritatis,
          totam maiores dolor necessitatibus voluptatem ea? Officia dolor
          commodi aliquid aliquam consectetur fugiat?
        </p>
      </div>
    </div>
    <div class="w-full bg-white p-10">
      <h1 class="text-center font-roboto font-bold text-3xl">
        Available Courses
      </h1>
      <div class="inline-flex w-full">
        <button
          id="orderByBtn"
          class="group relative w-40 border p-2 rounded-md bg-gray-200"
        >
          Order By
          <span class="px-2"></span>
          <i class="fas fa-arrow-down"></i>
          <div
            class="absolute z-10 w-full h-auto border shadow-md mt-2 left-0 bg-gray-200 rounded-lg transition-all opacity-0 group-hover:opacity-100"
          >
            <p
              class="bg-white hover:bg-gray-100 p-2"
              v-on:click="sortAscending"
            >
              Ascending
            </p>
            <p
              class="bg-white hover:bg-gray-100 p-2"
              v-on:click="sortDescending"
            >
              Descending
            </p>
          </div>
        </button>
        <button
          id="sortByBtn"
          class="group relative ml-auto w-40 border p-2 rounded-md bg-gray-200"
        >
          Sort By
          <span class="px-2"></span>
          <i class="fas fa-arrow-down"></i>
          <div
            class="absolute z-10 w-full h-auto border shadow-md mt-2 left-0 bg-gray-200 rounded-lg transition-all opacity-0 group-hover:opacity-100"
          >
            <p
              class="bg-white hover:bg-gray-100 p-2"
              v-on:click="sortLessons('subject')"
            >
              Subject
            </p>
            <p
              class="bg-white hover:bg-gray-100 p-2"
              v-on:click="sortLessons('location')"
            >
              Location
            </p>
            <p
              class="bg-white hover:bg-gray-100 p-2"
              v-on:click="sortLessons('price')"
            >
              Price
            </p>
            <p
              class="bg-white hover:bg-gray-100 p-2"
              v-on:click="sortLessons('spaces')"
            >
              Availability
            </p>
          </div>
        </button>
      </div>
      <br />
      <br />
      <div class="w-full text-center">
        <input
          type="search"
          placeholder="Search..."
          class="w-96 p-2 px-5 rounded-md border"
          v-model="search"
        />
      </div>
      <br />
      <br />
      <div v-if="searchLessonsHasLessons">
        <lessons-child
          :lessons="this.searchLessons"
          @addProduct="addToCart"
        ></lessons-child>
      </div>
      <div v-else>
        <lessons-child
          :lessons="this.lessons"
          @addProduct="addToCart"
        ></lessons-child>
      </div>
    </div>
  </div>
</template>

<script>
import LessonsChild from "./components/lessons.vue";
import CheckoutComponent from "./components/checkout.vue";

export default {
  name: "App",
  components: { LessonsChild, CheckoutComponent },
  data() {
    return {
      lessons: [],
      orderBy: 0,
      sortBy: "",
      cart: [],
      name: "",
      number: "",
      nameValidated: false,
      numberValidated: false,
      dataValidated: false,
      search: "",
      searchLessons: [],
    };
  },
  watch: {
    search: function (val) {
      console.log("searching");
      console.log("value: " + val);
      this.searchLessons = [];
      if (val == "" || val == null) {
        console.log("searching empty value");
        this.searchLessons = [];
      } else {
        fetch(
          `https://web-individual-coursework-2.herokuapp.com/search/${val}`,
          {
            method: "get",
            mode: "cors",
            headers: {
              "Content-Type": "application/json",
            },
          }
        )
          .then((res) => res.json())
          .then((data) => {
            console.log(data);
            // dt = data;
            this.searchLessons.push(data[0]);
            console.log(this.searchLessons);
          });
      }
      console.log(this.searchLessons);
    },
    number: function (val) {
      var phoneno = /^\d{10}$/;
      if (val.match(phoneno)) {
        this.numberValidated = true;
      } else {
        this.numberValidated = false;
      }
      this.isDataValidated();
    },
    name: function (val) {
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
  beforeMount() {
    this.fetchLessons();
  },
  methods: {
    fetchLessons: function () {
      fetch("https://web-individual-coursework-2.herokuapp.com/lessons/", {
        method: "get",
        mode: "cors",
        headers: {
          "Content-Type": "application/json",
        },
      })
        .then((res) => res.json())
        .then((data) => {
          console.log(data);
          this.lessons = data;
        });
    },
    onSubmitCheckout: function (dt) {
      this.cart.forEach((cartItem) => {
        console.log(cartItem);

        const orderDetails = {
          name: dt.name,
          phoneNo: dt.number,
          lessonId: cartItem.id,
          noOfSpaces: cartItem.quantity,
        };

        console.log(orderDetails);

        fetch("https://web-individual-coursework-2.herokuapp.com/order/", {
          method: "post",
          mode: "cors",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(orderDetails),
        }).then(() => {
          const updatedSpaces = {
            spaces: cartItem.spacesLeft,
          };
          fetch(
            `https://web-individual-coursework-2.herokuapp.com/lesson/${cartItem.id}`,
            {
              method: "put",
              mode: "cors",
              headers: {
                "Content-Type": "application/json",
              },
              body: JSON.stringify(updatedSpaces),
            }
          ).then((res) => console.log(res));
        });
      });

      this.cart = [];
      this.name = "";
      this.number = "";
      alert("Checkout Complete");
      this.hideSidebar();
    },
    showSidebar: function () {
      const sidebar = document.getElementById("sidebar");
      sidebar.style.visibility = "visible";
      sidebar.style.opacity = 1;
      sidebar.style.transform = "translateX(0)";
    },
    // hideSidebar: function () {
    //   const sidebar = document.getElementById("sidebar");
    //   sidebar.style.visibility = "invisible";
    //   sidebar.style.opacity = 0;
    //   sidebar.style.transform = "translateX(20rem)";
    // },
    addToCart: function (lesson) {
      if (lesson.spaces > 0) {
        console.log(this.lessons.indexOf(lesson));
        var found = false;
        var index = 0;
        var counter = 0;
        for (var c in this.cart) {
          console.log(this.cart[c].id);
          console.log(lesson._id);
          if (this.cart[c].id == lesson._id) {
            found = true;
            index = counter;
            console.log("found");
          }
          counter++;
        }
        if (found) {
          this.cart[index].quantity += 1;
          this.cart[index].spacesLeft -= 1;
        } else {
          this.cart.push({
            id: lesson._id,
            subject: lesson.subject,
            image: lesson.image,
            quantity: 1,
            spacesLeft: this.lessons[this.lessons.indexOf(lesson)].spaces - 1,
          });
        }
        this.lessons[this.lessons.indexOf(lesson)].spaces -= 1;
      } else {
        alert("No more items available");
      }
    },
    canAddToCart(lesson) {
      if (lesson.spaces > 0) {
        return true;
      } else {
        return false;
      }
    },
    sortAscending: function () {
      if (this.orderBy == 1) {
        console.log(this.sortBy);
        switch (this.sortBy) {
          case "subject":
            this.lessons.sort(function (a, b) {
              let x = a.subject.toLowerCase();
              let y = b.subject.toLowerCase();
              if (x < y) {
                return -1;
              }
              if (x > y) {
                return 1;
              }
              return 0;
            });
            break;
          case "location":
            this.lessons.sort(function (a, b) {
              let x = a.location.toLowerCase();
              let y = b.location.toLowerCase();
              if (x < y) {
                return -1;
              }
              if (x > y) {
                return 1;
              }
              return 0;
            });
            break;
          case "price":
            this.lessons.sort(function (a, b) {
              return a.price - b.price;
            });
            break;
          case "spaces":
            this.lessons.sort(function (a, b) {
              return a.spaces - b.spaces;
            });
            break;
          default:
            this.lessons.sort(function (a, b) {
              let x = a.subject.toLowerCase();
              let y = b.subject.toLowerCase();
              if (x < y) {
                return -1;
              }
              if (x > y) {
                return 1;
              }
              return 0;
            });
            console.log("default");
            break;
        }
        this.orderBy = 0;
        // this.displayProducts();
        this.$forceUpdate();
      }
    },
    sortDescending: function () {
      if (this.orderBy == 0) {
        switch (this.sortBy) {
          case "subject":
            this.lessons.sort(function (a, b) {
              let x = a.subject.toLowerCase();
              let y = b.subject.toLowerCase();
              if (x > y) {
                return -1;
              }
              if (x < y) {
                return 1;
              }
              return 0;
            });
            break;
          case "location":
            this.lessons.sort(function (a, b) {
              let x = a.location.toLowerCase();
              let y = b.location.toLowerCase();
              if (x > y) {
                return -1;
              }
              if (x < y) {
                return 1;
              }
              return 0;
            });
            break;
          case "price":
            this.lessons.sort(function (a, b) {
              return b.price - a.price;
            });
            break;
          case "spaces":
            this.lessons.sort(function (a, b) {
              return b.spaces - a.spaces;
            });
            break;
          default:
            this.lessons.sort(function (a, b) {
              let x = a.subject.toLowerCase();
              let y = b.subject.toLowerCase();
              if (x > y) {
                return -1;
              }
              if (x < y) {
                return 1;
              }
              return 0;
            });
            console.log("default");
            break;
        }
        this.orderBy = 1;
        console.log(this.lessons);
        // this.displayProducts();
        this.$forceUpdate();
        console.log("fired");
      }
    },
    sortLessons: function (sortBy) {
      switch (sortBy) {
        case "subject":
          this.sortBy = "subject";
          this.lessons.sort(function (a, b) {
            let x = a.subject.toLowerCase();
            let y = b.subject.toLowerCase();
            if (x < y) {
              return -1;
            }
            if (x > y) {
              return 1;
            }
            return 0;
          });
          break;
        case "location":
          this.sortBy = "location";
          this.lessons.sort(function (a, b) {
            let x = a.location.toLowerCase();
            let y = b.location.toLowerCase();
            if (x < y) {
              return -1;
            }
            if (x > y) {
              return 1;
            }
            return 0;
          });
          break;
        case "price":
          this.sortBy = "price";
          this.lessons.sort(function (a, b) {
            return a.price - b.price;
          });
          break;
        case "spaces":
          this.sortBy = "spaces";
          this.lessons.sort(function (a, b) {
            return a.spaces - b.spaces;
          });
          break;
        default:
          console.log("default");
          break;
      }
      // this.displayProducts();
      this.$forceUpdate();
    },
    deleteItemFromCart: function (index) {
      const lessonData = this.cart[index];
      console.log(lessonData);
      this.cart.splice(index, 1);
      for (var l in this.lessons) {
        if (this.lessons[l]._id == lessonData.id) {
          console.log("found");
          this.lessons[l].spaces += lessonData.quantity;
        }
      }
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
  },
  computed: {
    getLessons() {
      return this.lessons;
    },
    cartHasItems() {
      if (this.cart.length > 0) {
        return true;
      } else {
        return false;
      }
    },
    searchLessonsHasLessons() {
      if (this.searchLessons.length > 0) {
        console.log("has lessons");
        return true;
      } else {
        console.log("does not have lessons");
        return false;
      }
    },
    // searchLessonsDoesNotHaveLessons() {
    //   if (this.searchLessons.length == 0) {
    //     return true;
    //   } else {
    //     return false;
    //   }
    // },
  },
};
</script>

<style></style>

<template>
  <div>
    <link
      href="//netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap.min.css"
      rel="stylesheet"
      id="bootstrap-css"
    />

    <v-text-field
      label="Wyszukaj składnik"
      id="search"
      class="search"
      v-model="searchIngredient"
      filled
      rounded
    ></v-text-field>

    <div id="pagination-div">
      <b-pagination
        v-model="currentPage"
        align="right"
        pills
        :total-rows="rows"
        :per-page="perPage"
        size="lg"
      ></b-pagination>
    </div>

    <b-table-simple>
      <b-thead head-variant="dark">
        <b-tr>
          <b-th class="column-name">Lubiane składniki</b-th>
          <b-th class="column-name">Wszystkie</b-th>
          <b-th class="column-name">Nie lubiane składniki</b-th>
        </b-tr>

        <b-tr>
          <b-td>
            <li v-for="want in sortedWanted" :key="want" :value="want">
              {{ want }}
              <button
                type="button"
                v-on:click="returnToAll(want, 'wanted')"
                class="btn btn-warning btn-circle btn-lg"
              >
                <i class="glyphicon glyphicon-remove"></i>
              </button>
            </li>
          </b-td>

          <b-td>
            <ul>
              <li
                v-for="ingredient in SlicedIngredients"
                :key="ingredient"
                :value="ingredient"
              >
                {{ ingredient }}

                <button
                  type="button"
                  v-on:click="remove(ingredient)"
                  class="btn btn-warning btn-circle btn-lg"
                >
                  <i class="glyphicon glyphicon-remove"></i>
                </button>
                <button
                  value="testest"
                  type="button"
                  v-on:click="add(ingredient)"
                  class="btn btn-info btn-circle btn-lg"
                >
                  <i class="glyphicon glyphicon-ok"></i>
                </button>
              </li>
            </ul>
          </b-td>

          <b-td>
            <li
              v-for="notWanted in sortedNotWantedIngredients"
              :key="notWanted"
              :value="notWanted"
            >
              {{ notWanted }}
              <button
                type="button"
                v-on:click="returnToAll(notWanted, 'notWanted')"
                class="btn btn-warning btn-circle btn-lg"
              >
                <i class="glyphicon glyphicon-remove"></i>
              </button>
            </li>
          </b-td>
        </b-tr>
      </b-thead>
    </b-table-simple>
  </div>
</template>

<script>
export default {
  name: "IngredientsTable",
  props: ["ingredients"],

  data() {
    return {
      wanted: [],
      notWantedIngredients: [],
      perPage: 60,
      currentPage: 1,
      searchIngredient: ""
    };
  },
  methods: {
    add(ingredient) {
      this.wanted.push(ingredient);
      let localIngredients = this.ingredients.filter(function(e) {
        return e !== ingredient;
      });
      this.$emit("wanted", this.wanted);
      this.$emit("ingredients", localIngredients);

      return this.wanted;
    },
    remove(ingredient) {
      this.notWantedIngredients.push(ingredient);
      let localIngredients = this.ingredients.filter(function(e) {
        return e !== ingredient;
      });
      this.$emit("notWanted", this.notWantedIngredients);
      this.$emit("ingredients", localIngredients);

      return this.notWantedIngredients;
    },
    returnToAll(ingredient, arg) {
      let localIngredients = this.ingredients;
      localIngredients.push(ingredient);
      this.$emit("ingredients", localIngredients);

      if (arg === "wanted") {
        this.wanted = this.wanted.filter(function(e) {
          return e !== ingredient;
        });
        this.$emit("wanted", this.wanted);
        return this.wanted;
      } else if (arg === "notWanted") {
        this.notWantedIngredients = this.notWantedIngredients.filter(function(
          e
        ) {
          return e !== ingredient;
        });
        this.$emit("notWanted", this.notWantedIngredients);
        return this.notWantedIngredients;
      } else return 1;
    }
  },

  computed: {
    rows() {
      if (!this.ingredients) {
        return 0;
      }
      return this.ingredients.length;
    },
    SlicedIngredients() {
      let sortedIngredients = this.ingredients;

      if (!this.ingredients) {
        return null;
      }

      return sortedIngredients
        .filter(text => text.includes(this.searchIngredient))
        .sort()
        .slice(
          (this.currentPage - 1) * this.perPage,
          this.currentPage * this.perPage
        );
    },
    sortedWanted() {
      let sorted_wanted_ingredients = this.wanted;
      //return this.wanted.sort();
      return sorted_wanted_ingredients.sort();
    },
    sortedNotWantedIngredients() {
      let sortedNotWanted = this.notWantedIngredients;
      return sortedNotWanted.sort();
    }
  }
};
</script>

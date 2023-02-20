<template>
  <div class="container">
    <MultiRangeSlider
      :min="1970"
      :max="2023"
      :step="1"
      :ruler="true"
      :label="true"
      :minValue="barMinValue"
      :maxValue="barMaxValue"
      @input="UpdateValues"
    />
    <b-col lg="6" class="my-1">
      <b-form-group
        label="Filter"
        label-for="filter-input"
        label-cols-sm="3"
        label-align-sm="right"
        label-size="sm"
        class="mb-0"
      >
        <b-input-group size="sm">
          <b-form-input
            id="filter-input"
            v-model="filter"
            type="search"
            placeholder="Type to Search"
          ></b-form-input>

          <b-input-group-append>
            <b-button :disabled="!filter" @click="filter = ''">Clear</b-button>
          </b-input-group-append>
        </b-input-group>
      </b-form-group>
    </b-col>
    <b-table
      :no-local-sorting="true"
      striped
      hover
      :small="true"
      :items="filteredData"
      :fields="fields"
      :filter="filter"
      @sort-changed="sortChanged"
      :filter-included-fields="filterOn"
    ></b-table>
  </div>
</template>

<script>
import MultiRangeSlider from "multi-range-slider-vue";
import data from "./../../data.json";
export default {
  components: {
    MultiRangeSlider,
  },
  data() {
    return {
      barMinValue: 1970,
      barMaxValue: 2023,
      parsedData: data,
      fields: [
        {
          key: "id",
          sortable: true,
        },
        {
          key: "brand",
          sortable: true,
        },
        {
          key: "age",
          label: "Age",
          sortable: true,
        },
      ],
      filter: null,
      filterOn: [],
    };
  },
  methods: {
    UpdateValues(e) {
      this.barMinValue = e.minValue;
      this.barMaxValue = e.maxValue;
    },
    sortChanged(key) {
      fetch(`https://localhost:5173/cars/index?sort=${key}`, {
        method: "GET",
        mode: "no-cors",
        cache: 'no-cache',
        headers: {
          'Content-Type': 'application/json',
        }
      }).catch(result => {
        alert(`Запрос отправлен с параметром ${key.sortBy}`);
      })
    },
  },
  computed: {
    filteredData() {
      return this.parsedData.filter((item) => {
        if (
          item["age"] >= this.barMinValue &&
          item["age"] <= this.barMaxValue
        ) {
          return true;
        } else {
          return false;
        }
      });
    },
  },
};
</script>

<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.multi-range-slider {
  max-width: 600px;
  margin: 0 auto;
}
.container{
  max-width: 700px;
  margin: 0 auto;
}

</style>

<template>
  <div style="width:400px;">
        <SearchBar
            :placeholder="placeholder"
            :search-results="searchedItems"
            v-model="searchText"
            @enter-press="onEnter"
            @on-typing="onTyping"
            @option-select="selected"
            :on-select-hide-list=true
            :disabled=false
            add-class="extra-class"
            :is-searching="searching"
            :search-not-found="'Not Found: '+searchText"
            icon-search="fa fa-search"
            icon-cancel="fa fa-times"
            icon-loader="fas fa-circle-notch fa-spin"
        >
        </SearchBar>
  </div>
</template>
<script>
import SearchBar from 'SearchBar.vue'
export default {
  name: 'App',
  components: {
    SearchBar
  },
  data() {
    return {
      placeholder: 'What are you looking for ?',
      searchedItems: [],
      searchText: '',
      searching: false, //When it's true "Loader" icon will show on search box.
    }
  },
  methods: {
    onEnter() {
      //Function will run on enter key press.
      this.getData();
    },
    onTyping() {
      //Function will quickly run when user stop typing on search box.
      this.getData();
    },
    selected: function (value, text) {
      //Selected item "Value and Text" will receive in this function via params.
      console.log(value, text);
    },
    getData() {
      //Getting data from API and filling items in "searchedItems"
      let self = this;
      self.searching = true;
      fetch('https://www.thecocktaildb.com/api/json/v1/1/search.php?s=' + this.searchText)
          .then(response => response.json())
          .then(json => {
            self.searchedItems = [];
            let data = json.drinks;
            if (data != null) {
              data.forEach(function (obj) {
                console.log(obj);
                self.searchedItems.push({'value': obj.idDrink, 'text': obj.strDrink + ' ' + obj.strCategory})
              });
            }
            self.searching = false;
          });
    }
  }
}
</script>
<style scoped>
@import 'https://pro.fontawesome.com/releases/v5.10.0/css/all.css';
</style>
<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.searchButton {
  color: blue;
}
</style>

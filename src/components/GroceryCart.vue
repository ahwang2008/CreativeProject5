<template>
    <div id="app">
      
      <div>
          <h1>Mario's Shopping Kart
          <img src="http://windows7themes.net/wallpaper_large/mario-kart-7-wallpaper-3.jpg" height="50px" width="50px"></h1>
          <pre class="tab">Item:                   Store:                 Quantity:</pre>
          <p style="float:right;"><img src="https://emojipedia-us.s3.amazonaws.com/thumbs/160/emojidex/107/shopping-trolley_1f6d2.png" height="400px" width="400px"></p>
      </div>

      <form v-on:submit.prevent="addItem">
  <input type="text" v-model="text">
  <input type="text" v-model="text1">
  <input type="number" v-model="priority" min = "1" max = "50">
  <button type="submit">Add</button>
  <p v-show="activeItems.length === 0">You're Shopping Kart is empty! Maybe stock up on some bananas?
      <img src="https://vignette.wikia.nocookie.net/mariokart/images/b/b6/Triple_Bananas_Artwork_-_Mario_Kart_Wii.png/revision/latest?cb=20170323003819" align="middle" height = "50px" width="50px">
    </p>
      </form>
      <!--<div class="controls">
  <button v-on:click="showAll()">Show All</button>
  <button v-on:click="showActive()">Show Active</button>
  <button v-on:click="showCompleted()">Show Completed</button>
  <button v-on:click="deleteCompleted()">Delete Completed</button>
  <button v-on:click="sortItems()">Sort by Priority</button> 
      </div> -->
      <ul>
  <li v-for="item in filteredItems" draggable="true" v-on:dragstart="dragItem(item)" v-on:dragover.prevent v-on:drop="dropItem(item)">
    <input type="checkbox" v-model="item.completed" v-on:click="completeItem(item)" />
    <label v-bind:class="{ completed: item.completed }">{{ item.text }}</label>
    <label v-bind:class="{ completed: item.completed }">{{ item.text1 }}</label>
    <button v-on:click="upPriority(item)">Up</button>
    <button v-on:click="downPriority(item)">Down</button>
    {{ item.priority }}
    <button v-on:click="deleteItem(item)" class="delete">X</button>
  </li>
      </ul>
      
    </div>
 </template>

<script>
 //var app = new Vue({
   export default {
     name: 'GroceryCart',
  data() {
    return {
    items: [],
    text: '',
    text1: '',
    show: 'active',
    drag: {},
    sortCalled: true,
    //janky stuff
    priority: 1,
    item: '',
    completed: false
    }
  },
  created: function() {
    this.getItems();
  },
  computed: {
    activeItems: function() {
      return this.items.filter(function(item) {
      return !item.completed;
      });
    },
    filteredItems: function() {
      if (this.sortCalled) {
        return this.items.sort((a,b) => {if (a.text1 < b.text1)
          return -1
        if (a.text1 > b.text1)
          return 1
        return 0});
      }
      if (this.show === 'active')
        return this.items.filter(function(item) {
        return !item.completed;
      });
      /*
      if (this.show === 'completed')
        return this.items.filter(function(item) {
        return item.completed;
      });*/
      return this.items;
    },
    
  },

  methods: {
    addItem: function() {
      axios.post("/api/items", {
      text: this.text,
      text1: this.text1,
      priority: this.priority,
      completed: false
      }).then(response => {
      this.text = "";
      this.text1= "";
      this.getItems();
      return true;
      }).catch(err => {
      });
    },
    completeItem: function(item) {
      axios.put("/api/items/" + item.id, {
      text: item.text,
      text1: item.text1,
      completed: !item.completed,
      orderChange: false,
      }).then(response => {
      return true;
      }).catch(err => {
      });
    },
    upPriority: function(item) {
      axios.put("/api/items/" + item.id, {
      text: item.text,
      text1: item.text1,
      priority: item.priority,
      priorityUp: true,
      }).then(response => {
      this.getItems();
      return true;
      }).catch(err => {
      });
    },
    downPriority: function(item) {
      axios.put("/api/items/" + item.id, {
      text: item.text,
      text1: item.text1,
      priority: item.priority,
      priorityDown: true,
      }).then(response => {
      this.getItems();
      return true;
      }).catch(err => {
      });
    },
    deleteItem: function(item) {
      axios.delete("/api/items/" + item.id).then(response => {
      this.getItems();
      return true;
      }).catch(err => {
      });
    },
    getItems: function() {
      axios.get("/api/items").then(response => {
      this.items = response.data;
      return true;
      }).catch(err => {
      });
    },
    /*showAll: function() {
      this.show = 'all';
    },
    showActive: function() {
      this.show = 'active';
    },
    /*showCompleted: function() {
      this.show = 'completed';
    },
    sortItems: function() {
      this.sortCalled = true;
    },*/
    deleteCompleted: function() {
      this.items.forEach(item => {
      if (item.completed)
        this.deleteItem(item)
      });
    },
    dragItem: function(item) {
      this.drag = item;
    },
    dropItem: function(item) {
      axios.put("http://localhost:3000/api/items/" + this.drag.id, {
      text: this.drag.text,
      text1: this.drag.text1,
      completed: this.drag.completed,
      orderChange: true,
      orderTarget: item.id,
      priority: this.drag.priority,
      }).then(response => {
      this.getItems();
      return true;
      }).catch(err => {
      });
    },
  }
}
</script>

<style scoped>
body {
    font-family: 'Lucida Console';
    font-size: 16px;
    color: #c20316;
    padding: 20px 100px 0px 100px;
    background: rgb(255, 255, 176);
}

/* List */

ul {
    list-style: none;
}

li {
    background: white;
    width: 673px;
    min-height: 30px;
    padding: 10px;
    margin-bottom: 10px;
    font-size: 1em;
    display: flex;
    align-items: center;
}

.delete {
    display: block;
    margin-left: auto;
}

/*li:hover .delete {
    display: block;
}*/

label {
    width: 400px;
}

.completed {
    text-decoration: line-through;
}

/* Form */

input[type=checkbox] {
    transform: scale(1.5);
    margin-right: 10px;
}

input[type=text] {
    font-size: 1em;
}

button {
    font-family: 'Arvo';
    font-size: 1em;
}

/* Controls */

.controls {
    margin-top: 20px;
}
</style>
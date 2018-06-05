<template>
  <div id="app" class="container">

    <div class="page-header">
      <h1>Vue.js and Firebase Sample App</h1>
    </div>


    <div class="panel panel-default">
        <div class="panel-heading">
          <h3>Add New Books</h3></div>
        <div class="panel-body">
          <form id="create-book-form" v-on:submit.prevent="addBook">
            <div class="form-group">
                <label for="title">Title:</label>
                <input type="text" class="form-control" id="title" v-model="newBook.title" required/>
            </div>
              <div class="form-group">
                  <label for="author">Author:</label>
                  <input type="text" class="form-control" id="author" v-model="newBook.author" required/>
              </div>
              <div class="form-group">
                  <label for="isbn">ISBN:</label>
                  <input type="text" pattern="[0-9]{13}" class="form-control" id="isbn" v-model="newBook.isbn"/>
                  <span class="help-block">Optional. Must be 13 digits long.</span>
              </div>
              <div class="form-group">
                  <label for="url">URL:</label>
                  <input type="url" class="form-control" id="url" v-model="newBook.url" required/>
              </div>
              <input type="submit" class="btn btn-primary" value="Add Book"/>
          </form>
        </div>
    </div>

    <div class="panel panel-default">
        <div class="panel-heading clearfix">
            <h3 class="pull-left">Book List</h3>
            <input class="form-control pull-right" id="search-input" v-on:keyup="filter"
                    type="text" placeholder="Search by Title">
        </div>
        <div class="panel-body">
        <div class="table-responsive-sm">
            <table id="list" class="table table-hover table-striped sortable">
              <thead>
                <tr>
                  <th> <button class="sort-button" v-on:click="sortBy('0')">Title</button></th>
                  <th> <button class="sort-button" v-on:click="sortBy('1')">Author</button></th>
                  <th> <button class="sort-button" v-on:click="sortBy('2')">ISBN</button></th>
                  <th> <button disabled class="sort-button" style="color:transparent">I</button></th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="book in books">
                  <td> <a v-bind:href="book.url">{{ book.title }}</a></td>
                  <td> {{ book.author }}</td>
                  <td> {{ book.isbn }}</td>
                  <td><span style="cursor: pointer;" class="glyphicon glyphicon-trash pull-right" aria-hidden="true" v-on:click="deleteBook(book)"></span></td>
                </tr>
              </tbody>
            </table>
        </div>
        </div>

    </div>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld'

import Firebase from 'firebase'
import toastr from 'toastr'

let config = {
    apiKey: "AIzaSyAR_ZFK9Ck0OZcL404Lyonu-2zVPLb9QdE",
    authDomain: "vuejs-firebase-booklist.firebaseapp.com",
    databaseURL: "https://vuejs-firebase-booklist.firebaseio.com",
    projectId: "vuejs-firebase-booklist",
    storageBucket: "vuejs-firebase-booklist.appspot.com",
    messagingSenderId: "222937484301"
}

let app = Firebase.initializeApp(config);
let db = app.database();

let bookRef = db.ref('books');

let sortName = ''
let sortDir = 'asc'


export default {
  name: 'App',
  firebase: {
      books: bookRef
  },
  data () {
    return {
      newBook: {
        title: '',
        author: '',
        isbn: '',
        url: ''
      }
    }
  },
  methods: {

    addBook: function() {
        bookRef.push(this.newBook);
        this.newBook.title = '';
        this.newBook.author = '';
        this.newBook.isbn = '';
        this.newBook.url = '';
    },

    deleteBook: function(book) {
        bookRef.child(book['.key']).remove();
        toastr.success('Book removed successfully')
    },

    filter: function() {
      var input, table, tr, td, i;
      input = document.getElementById('search-input').value.toUpperCase();
      table = document.getElementById("list");
      tr = table.getElementsByTagName("tr");

      for (i = 0; i < tr.length; i++) {
          td = tr[i].getElementsByTagName("td")[0];
          if (td) {
              if (td.textContent.toUpperCase().startsWith(input)) {
                  tr[i].style.display = "";
              } else {
                  tr[i].style.display = "none";
              }
          }
      }
    },

    sortBy: function(n) {
        var table, rows, switching, i, x, y, shouldSwitch, dir, switchcount = 0;
        table = document.getElementById("list");
        switching = true;
        dir = "asc";
        while (switching) {
          switching = false;
          rows = table.getElementsByTagName("tr");

          for (i = 1; i < (rows.length - 1); i++) {

            shouldSwitch = false;

            x = rows[i].getElementsByTagName("td")[n];
            y = rows[i + 1].getElementsByTagName("td")[n];

            if (dir == "asc") {
              if (x.innerHTML.toLowerCase() > y.innerHTML.toLowerCase()) {
                shouldSwitch = true;
                break;
              }
            } else if (dir == "desc") {
              if (x.innerHTML.toLowerCase() < y.innerHTML.toLowerCase()) {
                shouldSwitch = true;
                break;
              }
            }
          }
          if (shouldSwitch) {

            rows[i].parentNode.insertBefore(rows[i + 1], rows[i]);
            switching = true;

            switchcount ++;
          } else {

            if (switchcount == 0 && dir == "asc") {
              dir = "desc";
              switching = true;
            }
          }
        }
      },
    }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}

input:-webkit-autofill {
    -webkit-box-shadow: 0 0 0px 1000px white inset;
}

table {
    font-family: arial, sans-serif;
    table-layout: fixed;
}

th {
    font-size: 15px;
}

.sort-button {
     background-color: inherit;
     border: none;
     cursor: pointer;
     margin-left:-6px;
     outline: none;
}

.panel-heading .form-control {
    height: 80%;
    width: 30%;
    margin-top: 17px;
}

.panel {
    margin-top: 2%;
}

</style>

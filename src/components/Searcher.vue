<template>
  <div class="header">
    <h1>GitHub repositories searcher</h1>
    <input
      v-model="request"
      type="text"
      @input="
        () => {
          requestToDebouncer(1);
        }
      "
      placeholder="Search..."
    />

    <div class="results-for-searching">Results for searching: {{ total }}</div>

    <div v-if="total !== 0">
      {{ requestPage }} page from {{ Math.ceil(total / 30) }}
    </div>
    <div v-else></div>
  </div>
  <div class="flex-head">
    <div id="th1">author</div>
    <div id="th2">name</div>
    <div id="th3">description</div>
    <div id="th4">language</div>
    <div id="th5"><i class="far fa-star"></i></div>
  </div>

  <div v-for="value in object" class="flex">
    <div id="th1">
      <a :href="value.owner.html_url" target="_blank">{{
        value.owner.login
      }}</a>
    </div>
    <div id="th2">{{ value.name }}</div>
    <div id="th3">{{ value.description }}</div>
    <div id="th4">{{ value.language }}</div>
    <div id="th5">{{ value.stargazers_count }}</div>
  </div>
  <div v-if="total !== 0">
    <input
      id="pageNum"
      @input="
        () => {
          func(value);
        }
      "
      :value="requestPage"
      type="number"
      min="0"
      :max="Math.ceil(total / 30)"
    />
    page from
    {{ Math.ceil(total / 30) }}
  </div>
  <div v-else></div>
</template>

<script>
import axios from "axios";

export default {
  name: "Searcher",
  props: {
    msg: String,
  },
  methods: {
    requestToDebouncer(e) {
      //This code validate input. It can't be less then 0 and more than pages available
      if (!e) {
        if (document.getElementById("pageNum").value < 1) {
          document.getElementById("pageNum").value = 1;
        }
        if (
          document.getElementById("pageNum").value > Math.ceil(this.total / 30)
        ) {
          document.getElementById("pageNum").value = Math.ceil(this.total / 30);
        }
        this.requestPage = document.getElementById("pageNum").value;
      } else {
        this.requestPage = 1;
      }
      // End of validation

      //debouncer (wait users input to avoid error 403 - to many requests)
      this.i++;
      setTimeout(() => {
        this.j++;
      }, 500);
    },

    func(e) {
      axios
        .get(
          `https://api.github.com/search/repositories?q=${this.request}&page=${this.requestPage}`
        )
        .then((response) => {
          this.total = response.data.total_count;
          this.object = response.data.items;
        })
        //error processing
        .catch((err) => {
          if (err.message === "Request failed with status code 403") {
            alert("To many requests!");
          }
          if (err.message === "Request failed with status code 422") {
            console.log("Blank request. Just write something:)");
          }
        });
    },
  },
  data() {
    return {
      total: 0,
      object: 0,
      requestPage: 1,
      //debouncer's variable
      i: 0,
      j: 0,
    };
  },
  mounted() {
    //debouncer (wait users input to avoid error 403 - to many requests)
    setInterval(() => {
      if (this.i === this.j && this.i !== 0) {
        this.i = 0;
        this.j = 0;
        this.func();
      }
    }, 600);
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1 {
}

ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
}
a {
  color: #42b983;
}
table {
  font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
  font-size: 14px;
  border-collapse: collapse;
  text-align: center;

  table-layout: fixed;
}

th,
td:first-child {
  background: #afcde7;
  color: white;
}
th,
td {
  width: 8vw;
  border-style: solid;
  border-width: 0 1px 1px 0;
  border-color: white;
  word-wrap: break-word;
}
td {
  background: #d8e6f3;
}
th:first-child,
td:first-child {
  text-align: left;
}

.flex,
.flex-head {
  display: flex;
  margin-right: 3%;
  margin-left: 3%;
  word-wrap: break-word;
  word-break: break-all;
}
.flex-head > div {
  padding: 10px;
  background-color: #bae0c1;
  border: 1px solid #aebdb6;
}
.flex > div {
  padding: 10px;
  background-color: #ebfff5;
  border: 1px solid #aebdb6;
}

#th1,
#th4 {
  width: 15%;
}

#th5 {
  width: 8%;
}

#th2 {
  width: 17%;
}

#th3 {
  width: 45%;
}

input {
  margin: 20px;
  width: 50%;
  height: 26px;
  font-size: 24px;
}

.header {
  background-color: #bae0c1;
  height: 24vh;
  margin-bottom: 7vh;
  padding: 15px;
}

#pageNum {
  width: auto;
  margin-right: 0;
}

@media (max-width: 480px) {
  h1 {
    font-size: 20px;
  }
}
</style>

<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <h2>CS527</h2>

    <textarea rows="8" cols="36" v-model="query" placeholder="Input your sql query here~">
    </textarea>
    <ul>
      <li>
      <p>DBMS type: </p>
      </li>
      <input type="radio" id="rds_radio" value="rds" v-model="picked">
      <label for="rds_radio">RDS</label>
      <input type="radio" id="redshift_radio" value="Redshift" v-model="picked">
      <label for="redshift_radio">Redshift</label>
      <br>
    </ul>
    <ul>
    <button v-on:click="queryFunc">Query now!</button>
    </ul>
    <ul>
    <br>
    <el-card class="result-card" style="width: 60%; margin: auto; font-size: 20px;">
      <ul>
        <div slot="header" class="result-card-header">
          <span>Query result</span>
        </div>
        <li>
        <label style="position: relative; left: 250px;">Time cost: </label>
        </li>
        <li>
        <p style="background-color=#BBBBBB; position: relative; left: 230px;">{{timeCost}}</p>
        </li>
      </ul>
      <el-table :data="result" stripe border style="width: 100%; margin: auto; font-size: 18px;" :header-cell-style="tableHeaderStyle">
        <div v-for="(item, i) in columns" :key="i">
          <el-table-column :label="item" :render-header="linefeed" :prop="item" :index="i">
          </el-table-column>
        </div>
      </el-table>
    </el-card>

    </ul>
    <br>
    <el-divider></el-divider>
    <br>
    <h2>Group members</h2>
    <ul>
      <li>
        Weizhi Wang<br>ww351
      </li>
      <li>
        Yanhan Zhang<br>yz963
      </li>
      <li>
        Zhenpeng Shen<br>zs293
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Welcome to the web sql retriever of Group 9',
      query: '',
      picked: 'rds',
      columns: [],
      result: [],
      timeCost: '',
      maxColLens: []
    }
  },
  created () {
  },
  methods: {
    linefeed (h, {column, index}) {
      let number = this.maxColLens[column.index]
      let size = 12
      column.minWidth = number * size
      return h('div', {class: 'table-head', style: {width: '100%'}}, [column.label])
    },
    tableHeaderStyle ({ row, column, rouIndex, columnIndex }) {
      return 'background-color: #FAFAFA; color: #333333;'
    },
    queryFunc: function () {
      function mapLen (str) {
        return str.length
      }
      console.log(this.picked)
      console.log(this.query)
      if (this.query !== '') {
        axios.post('backend/sql/query', {
          db_type: this.picked,
          query: this.query
        }, {
          headers: {
            'Access-Control-Allow-Origin': '*'
          }
        }).then(res => {
          this.columns = res.data.data.columns
          this.result = res.data.data.result
          this.timeCost = res.data.data.time_cost
          this.maxColLens = this.columns.map(mapLen)
          for (let r in this.result) {
            for (let c in this.columns) {
              if (this.result[r][this.columns[c]].length > this.maxColLens[c]) {
                this.maxColLens[c] = this.result[r][this.columns[c]].length
              }
            }
          }
        })
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
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
textarea {
  font-size: 26px;
}
p {
  font-size: 20px;
}
input {
  font-size: 30px;
}
button {
  height: 50px;
  width: 500px;
  font-size: 30px;
  background-color: #41a1f8;
}
</style>

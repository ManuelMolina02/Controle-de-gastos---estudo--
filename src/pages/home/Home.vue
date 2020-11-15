<template>

<div class="layout-home">

<div class="card-container">
  <div class="card-content">
    <h1 class="title-card">Gasto Total Cadastrado foi de:</h1>
    <p class="money" v-money-format="totals.totalSpent"/>
    <h5>Em um total de {{ totals.count }} compras</h5>
  </div>
</div>

<div class="card-container">
  <div class="card-content">
    <h1 class="title-card">A média de Gastos é de:</h1>
    <p class="money" v-money-format="totals.average"/>
  </div>
</div>

<div class="card-container">
  <div class="card-content">
    <h1 class="title-card">O maior gasto foi:</h1>
    <p class="money" v-money-format="totals.biggest.value"/>
  </div>
</div>

<div class="card-container">
  <div class="card-content">
    <h1 class="title-card">O menor gasto foi:</h1>
    <p class="money" v-money-format="totals.lowest.value"/>
  </div>
</div>

</div>
<!--
<table class="content-table">

  <thead>
    <tr>
      <th scope="col">Você gastou:</th>
      <th scope="col">Uma média de:</th>
      <th scope="col">O maior gasto foi:</th>
      <th scope="col">O menor gasto foi:</th>
    </tr>
  </thead>
   <tbody>
    <tr>
       <td class="money" v-money-format="totals.totalSpent"/>
       <td class="money" v-money-format="totals.average"/>
       <td class="money" v-money-format="totals.biggest.value"/>
       <td class="money" v-money-format="totals.lowest.value"/>
    </tr>
    <tr>
       <td>{{ totals.count }}</td>
       <td>{{ totals.biggest.type }}</td>
       <td v-date-format="totals.biggest.createdAt"/>
       <td v-date-format="totals.lowest.createdAt"/>
    </tr>
  </tbody>
</table> -->

</template>

<script>

export default {
  name: 'Home',

  data: () => ({
    expenses: []
  }),

  created () {
    this.getData()
  },
  computed: {
    totals () {
      const { expenses: exp } = this
      const values = {
        totalSpent: 0,
        count: 0,
        average: 0,
        biggest: {},
        lowest: {}
      }

      if (exp.length) {
        values.totalSpent = exp.map(e => +e.value)
          .reduce((acc, cur) => acc + cur, 0)

        values.count = exp.length

        values.average = values.totalSpent / exp.length

        values.biggest = exp.sort((a, b) => +b.value - +a.value)[0]
        values.lowest = exp.sort((a, b) => +a.value - +b.value)[0]
      }

      return values
    }
  },

  methods: {
    getData () {
      const ref = this.$firebase.database().ref(`/${window.uid}`)

      ref.on('value', data => {
        const values = data.val()
        this.expenses = Object.keys(values).map(i => values[i])

        console.log(this.expenses)
      })
    },
    onSlideStart (slide) {
      this.sliding = true
    },
    onSlideEnd (slide) {
      this.sliding = false
    }
  }
}
</script>

<style lang="scss" scoped>

.layout-home {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: 1fr 1fr;
  gap:2rem;
  padding: 2rem;
}

.card-container{
  border-radius: 16px;
  padding: 10px;
}

.card-container{
  display: block;
  text-align: center;
  align-items: center;
  background-color:#7159c1d7;
  border: 2px solid var(--featured-dark);
  color: rgba(255, 255, 255, 0.473);
  transition: .2s linear .2s;
  &:hover{
    color: rgba(255, 255, 255, 0.89);
    background-color: var(--featured);

  }
}

.card-container h1{
  font-weight:bold;
font-size: 1.2rem;
}

.card-container p{
  font-size: 1.8rem;
  color: rgb(255, 255, 255);
  border: 2px solid transparent;
  border-radius: 16px;
  background-color:#603dd38f;
  width: 60%;
  margin: .8rem auto;
}

.card-container h5{
  font-size: 1rem;
}

/*
.content-table {
  border-collapse: collapse;
  margin: 25px auto;
  font-size: .9em;
  min-width: 800px;
  border-radius: 5px 5px 0 0;
  overflow: hidden;
}
.content-table td:first-child {
  width: 240px
}

.content-table td {
  width: 190px
}

.content-table thead tr {
  background-color:#212529;
  color: white;
  text-align: left;
  overflow: hidden;

}

.content-table th,
.content-table td {
  padding: 12px 15px;
}

.content-table tbody tr {
  border-bottom: 1px solid rgb(90, 90, 90);
  background-color:#2c3034;
}

.content-table tbody tr:nth-of-type(even){
  background-color:#212529;
}

.content-table tbody tr:last-of-type{
  border-bottom: 2px solid  rgb(116, 116, 116);
} */

</style>

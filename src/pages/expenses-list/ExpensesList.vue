<template>
  <div>
    <div class="months-navigation">
      <div
        :key="i"
        class="month-link"
        @click="setActiveMonth(month)"
        v-for="(month, i) in groupedMonths"
        :class="{active: month.month === activeMonth.month}"
      >
        <div class="month-label">{{ month.month }}</div>
        <div class="value-label" v-money-format="month.total"/>
      </div>
    </div>

    <div class="container-group">
      <div class="container">
        <div v-if="activeMonth.data && !activeMonth.data.length">
          Você não cadastrou nenhum neste mês
        </div>
        <template v-else>
            <table class="content-table">
            <thead>
              <tr>
               <th scoped="col">Suas despesas foram:</th>
               <th scoped="col">Tipo de Gasto:</th>
               <th scoped="col">Recibo:</th>
               <th scoped="col">Data de Inserção</th>
               <th scoped="col">Valor:</th>
              </tr>
            </thead>
             <tbody>
                 <tr :key="data" :data="item" v-for="data in activeMonth.data">
                           <td> {{data.description}} </td>
                           <td> {{ data.type }} </td>
                           <td>
                             <button
                               v-if="data.receipt"
                               @click="openReceipt()"
                               class="btn-receipt"
                             >
                              <i class="fa fa-paperclip"></i>
                               Ver comprovante
                             </button>
                           </td>
                           <td class="data-table" v-date-format-two="data.createdAt"/>
                           <td  v-money-format="data.value"/>
                        </tr>
            </tbody>
          </table>
        </template>
      </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment'
import groupBy from 'lodash.groupby'

export default {
  name: 'ExpensesList',
  // components: {
  //   ExpenseListItem
  // },
  props: {
    data: { type: Object, required: true }
  },
  data: () => ({
    expenses: [],
    activeMonth: {}
  }),
  created () {
    this.getData()
  },
  mounted () {
    this.setActiveMonth()
  },

  computed: {
    groupedMonths () {
      let groupedMonths = []
      const addCurrentMonth = () => {
        groupedMonths.push({
          data: [],
          total: 0,
          month: moment().format('MM/YYYY')
        })
      }
      if (this.expenses.length) {
        const months = groupBy(this.expenses, i => (
          moment(i.createdAt).format('MM/YYYY')
        ))
        const sortedMonths = Object.keys(months).sort((a, b) => {
          const pattern = 'MM/YYYY HH'
          return moment(`${a} 01`, pattern).isBefore(moment(`${b} 01`, pattern))
            ? -1
            : +1
        })
        groupedMonths = sortedMonths.map(month => ({
          month,
          data: months[month],
          total: months[month].map(i => +i.value).reduce((a, c) => a + c, 0)
        }))
        const lastMonth = moment(groupedMonths[groupedMonths.length - 1].month, 'MM/YYYY')
        if (!lastMonth.isSame(moment(), 'month')) {
          addCurrentMonth()
        }
      } else {
        addCurrentMonth()
      }
      return groupedMonths
    }
  },
  methods: {
    getData () {
      const ref = this.$firebase.database().ref(`/${window.uid}`)
      ref.on('value', snapshot => {
        const values = snapshot.val()
        this.expenses = Object.keys(values).map(i => values[i])
      })
    },
    setActiveMonth (month = null) {
      this.activeMonth = month || this.groupedMonths[this.groupedMonths.length - 1]
    },
    openReceipt () {
      window.open('https://firebasestorage.googleapis.com/v0/b/expenses-24e37.appspot.com/o/HTumEFacUAdokg0OorMuapckMkp1%2FDesenho%20sem%20t%C3%ADtulo-1605205460474.svg?alt=media&token=8e04b2f9-c749-4168-a4a5-6f96d9b6dc76', '_blank')
    }
  }
}
</script>

<style lang="scss" scoped>

.months-navigation {
  display: flex;
  margin-left: -15px;
  width: calc(100% + 30px);
  background-color: var(--featured-dark);
  .month-link {
    padding: 15px;
    cursor: pointer;
    transition: .4s;
    text-align: center;
    &:hover {
      background-color: var(--featured);
    }
    &.active {
      background-color: var(--featured);
    }
    .month-label{
      font-weight: bold;
    }
    .value-label {
      color: rgb(223, 223, 223);
      font-size: 10pt;
    }
  }
}
.container-group {
  margin-left: -15px;
  overflow: hidden auto;
  width: calc(100% + 30px);
  height: calc(100vh - 69px);
}
.btn-receipt {
  display: block;
  min-width: 120px;
  border: none;
  background-color: #7159c1;
  color: white;
  border-radius: 25px;
  margin: auto;
  padding: 7px;
      &:hover {
      background-color: var(--featured-dark);
    }
}

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
}

</style>

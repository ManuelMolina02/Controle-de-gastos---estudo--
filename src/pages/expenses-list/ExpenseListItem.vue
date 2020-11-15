<template>

<table class="table table-striped table-dark">

  <thead>
    <tr>
      <th>Despesa</th>
      <th>Tipo de Gasto:</th>
      <th>Recibo:</th>
      <th>Data de Inserção</th>
      <th>Valor:</th>
    </tr>
  </thead>
   <tbody>
    <tr :key="index" v-for="(item,index) in activeMonth.data">
       <td> {{ data.description }} </td>
       <td> {{ data.type }} </td>

       <td>
         <button
            v-if="data.receipt"
            class="btn btn-sm btn-outline-primary"
            @click="openReceipt()">
              <i class="fa fa-paperclip"/>
                Recibo
          </button>
       </td>
       <td class="data-table" v-date-format-two="data.createdAt"/>
       <td class="col-2" v-money-format="data.value"/>
    </tr>

  </tbody>
</table>

<!-- <div class="expense-list-item row">
    <div class="col-10">
        <div class="description">
            <span>{{ data.description }}</span>
            <span>{{ data.type }}</span>
            <button
            v-if="data.receipt"
            class="btn btn-sm btn-outline-primary"
            @click="openReceipt()">
              <i class="fa fa-paperclip"/>
                Recibo
            </button>
        </div>
    <div class="data-table" v-date-format-two="data.createdAt"/>
</div>
<div class="col-2" v-money-format="data.value"/>
</div> -->
</template>

<script>
export default {
  props: {
    data: { type: Object, required: true }
  },
  methods: {
    openReceipt () {
      window.open(this.data.receipt, '_blanck')
    }
  }

}
</script>

<style lang="scss" scoped>
 .expense-list-item{
     border-bottom: 1px solid var(--dark-medium);
     padding: 10px 0;
 }
 .data-table{
     color: var(--light-low);
     font-size: 15px;
 }
 .description{
     display: flex;
     justify-content: space-between;
     align-items: center;
 }
</style>

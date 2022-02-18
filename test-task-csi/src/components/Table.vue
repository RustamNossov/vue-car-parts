<template>
  <div>
    <b-table id="my-table" class="main-tabel" sticky-header primary-key="id" striped hover :items="tableElements" :fields="fields">
            <template #cell(name)="data" @click="onFirst" class="first-col" >
               <span v-html="data.item.name"></span> 
            </template>

            <!-- A custom formatted column -->
            <template #cell(price)="data">
                {{ data.item.price }}
            </template>

            <!-- A virtual composite column -->
            <template #cell(amount)="data">
                {{ data.item.amount }}
            </template>

            <!-- Optional default data cell scoped slot -->
            <template #cell(summa)="data">
                {{ data.item.summa }}
            </template>

            <template #cell(actions)="data">
                <div class="table__buttons-group">
                    <b-button 
                        :data-record-id="data.item.id" 
                        data-action="delete" 
                        v-b-modal.modal-1 
                        @click="onClickButton"
                        variant="danger"
                    >Удалить</b-button>
                    <b-button 
                        :data-record-id="data.item.id"
                        variant="success" 
                        data-action="add" 
                        v-b-modal.modal-1 
                        @click="onClickButton"
                    >Добавить</b-button>
                </div>
            </template>



    </b-table>
    <div class="table__message" v-if="tableElements.length === 0 && loadingStatus === 'idle'">Не найдено ни одной детали.</div>
    <div class="table__loading-wrapper" v-if="loadingStatus === 'loading'"><Spinner/></div>
    <b-alert  v-if="loadingStatus === 'error'" show variant="danger">Возникла ошибка. Повторите позже.</b-alert>

  </div>
</template>

<script>
    import Spinner from './Spinner.vue'

  export default {
    name: 'Table',
    props: {
        onTransferProps: Function,
        tableElements: Array,
        loadingStatus: String
    },
    components: {
        Spinner
    },
    data() {
      return {
        // Note `isActive` is left out and will not appear in the rendered table
        fields: [
            { key: 'name', label: 'Деталь', tdClass: 'first-col', thClass: 'first-col-header' },
            { key: 'price', label: 'Цена', tdClass: 'sec-col', thClass: 'sec-col-header' },
            { key: 'amount', label: 'Количество', tdClass: 'third-col', thClass: 'third-col-header' },
            { key: 'summa', label: 'Стоимость', tdClass: 'forth-col', thClass: 'forth-col-header' },
            { key: 'actions', label: 'Действия', tdClass: 'fifth-col', thClass: 'fifth-col-header' }
        ]
      }
    },
    methods: {
        onClickButton(event) {
            
            const recordId = event.target.getAttribute("data-record-id")
            const actionType = event.target.getAttribute("data-action")
            this.onTransferProps(recordId, actionType)
        }
        
    }
  }
  






</script>

<style>
    .main-tabel{
        margin-top: 1vh;
        max-height: 69vh !important;
        width: 100%;
    }
    .table__buttons-group {
        float: right;
       
    }
    .table__buttons-group button {
        margin-right: 30px;
    }
    .fifth-col-header {
         width: 30%;
        text-align: center;
    }
    .table__message {
        text-align: center;
        font-size: 1.5rem;
        font-style: italic;
        color: rgb(85, 84, 84);
    }
    .table__loading-wrapper {
        height: 40vh;
        width: 100%;
        background-color: rgba(0, 0, 0, 0.041);
    }
     



</style>
<template>
    <div class="main">
        <h1 class="main__title">Детали автомобиля</h1>
        <h2 class="main__subtitle">Итоговая стоимость: {{totalSum}}</h2>
        <b-button 
            v-b-modal.modal-1 
            @click="onTransferProps(null, 'add')" 
            >Добавить деталь верхнего уровня
        </b-button>

        <b-button-group>
            <b-button variant="outline-primary" 
                @click="onExportToExcel"
                >Скачать PDF</b-button>
            <b-button variant="outline-primary">
                <download-excel 
                    :data="tableElements"
                    :fields="fields"
                    name="Детали"
                    header="Детали автомобиля"
                    :footer="`Итоговая стоимость: `+ totalSum"
                    >
                    Скачать Excel
                </download-excel>
            </b-button>
        </b-button-group>
    </div>
</template>


<script>




export default {
    name: 'MainPage',
    props: {
        onTransferProps: Function,
        onExportToExcel: Function,
        tableElements: Array
    },
    data: () => ({
        fields: {
                "Деталь": "name",
                "Цена": "price",
                "Количество": "amount",
                "Стоимость" : "summa"
        }
    }),
    computed: {
        totalSum: function () {
             const sum = this.tableElements.length > 0 ?  this.tableElements
                                                        .filter(item=>{return item.level=="1"})
                                                        .reduce((sum, item)=>{
                                                                const itemSumma = typeof item.summa === "string" ? +item.summa : item.summa
                                                                return sum + itemSumma}, 0) : '0' 
        return sum
    }
    }
}
</script>

<style>
    .main {
        height: 24vh;
    }
    .btn-group {
        float: right;
    }

</style>
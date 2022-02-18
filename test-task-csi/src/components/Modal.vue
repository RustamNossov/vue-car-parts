<template>
    <b-modal 
        id="modal-1" 
        :title="actionType === 'delete' ? 'Удаление детали' : 'Добавление детали'"
        @ok="actionType === 'delete' ? 
                            onHandleOk(record.id, record.level)
                            : null"
        :ok-only="actionType === 'add' ? true : false"
        :ok-variant="actionType === 'add' ? 'secondary' : 'primary'"
        :ok-title="actionType === 'add' ? 'Cancel' : 'Ok'"
        >
         <b-alert  v-if="loadingStatus === 'error'" show variant="danger">Возникла ошибка. Повторите позже.</b-alert>

                <p class="my-4" v-if="actionType === 'delete'">
                    Вы уверены что хотите удалить деталь
                    <strong>"{{record.name}}"</strong>
                    <span v-if="record.level !== '3'"> и все ее дочерние элементы?</span>
                    <span v-else>?</span>
                </p>
                <div v-if="actionType === 'add'">
                    <p class="my-4">
                        Введите данные для добавления детали.
                        <span v-if="recordId && record.level !== '3'"> (родитель: {{record.name}}) </span>
                    </p>
                    <Form :record="record" :addNewRecord="addNewRecord"/>
                </div>
        
<div class="modal__loading-wrapper" v-if="loadingStatus === 'loading'" ><Spinner/></div> 
    </b-modal>
    

</template>

<script>
import Form from './Form.vue'
import Spinner from './Spinner.vue'
export default {
    name: 'Modal',
    components: {
        Form, Spinner
    },
    props: {
        elementsForUpdate: Array,
        actionType: String,
        recordId: String,
        onHandleOk: Function,
        addNewRecord: Function,
        loadingStatus: String
    },
    methods: {
    },
    
    computed: {
        record: function () {
            const currRecord = this.recordId ? this.elementsForUpdate.filter(item=>item.id === this.recordId)[0]  : {}
                                    
        return currRecord
        }
    } 
  

    
}

</script>

<style >

.modal__loading-wrapper {
    background: rgba(0, 0, 0, 0.253);
    position: absolute;
    top: 0;
    left: 0;
    height: 100%;
    width: 100%;
    
} 
.modal__loading-wrapper svg {
    position: relative;
    top: 50%;
    transform: translateY(-50%);
}

</style>

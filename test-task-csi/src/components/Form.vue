<template>
    <FormulateForm name="myForm" v-model="formValues" @submit="handleSubmit">
        <FormulateInput
            name="name"
            label="Введите название детали (без номера)"
            validation="required|max:40"
            :validation-messages="{
                required: 'Обязательное поле',
                max: 'Максимальная длина 40 символов'
            }"
        />
        <FormulateInput
            name="price"
            label="Введите цену"
            validation="required|number|min:1"
            :validation-messages="{
                required: 'Обязательное поле',
                number: 'Введите число больше 1',
                min: 'Введите число больше 1',
            }"
        />
        <FormulateInput
            name="amount"
            label="Введите количество"
            validation="required|number|min:1"
            :validation-messages="{
                required: 'Обязательное поле',
                number: 'Введите число больше 1',
                min: 'Введите число больше 1',
            }"
        />

        <FormulateInput
            type="submit"
            label="Добавить деталь"
        />
        
    </FormulateForm>
</template>


<script>
import { v4 as uuidv4 } from "uuid";

export default {
    name: 'Form',

    props: {
        record: Object,
        addNewRecord: Function,
    },
    data: ()=>({
        formValues: {}
    }),
    methods: {
        handleSubmit() {
                // console.log()
            this.addNewRecord(this.prepareNewRecord())
            
            this.$formulate.reset('myForm')
            
        }, 
        prepareNewRecord() {
                console.log(this.record.level)
                const newRecord = {
                    id: uuidv4(), 
                    level: !Object.keys(this.record).length ? "1" : this.record.level == "3" ? "3" : +this.record.level+ 1,
                    parent: !Object.keys(this.record).length ? null: this.record.level == "3" ? this.record.parent : this.record.id,
                    name: this.formValues.name,
                    creationDate: Date.now(),
                    price: !Object.keys(this.record).length ? 0 : this.formValues.price,
                    amount: this.formValues.amount,
                    summa: !Object.keys(this.record).length ? 0 : +this.formValues.price * +this.formValues.amount
                }
                return newRecord
           

            
            
        }
    }

    
}

</script>

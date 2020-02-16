<template>
<div>
    <div v-if="condition.datatype==='string'">
        <v-text-field v-model="textValue" @change="onChange" type="text"></v-text-field>
    </div>
    <div v-else-if="condition.datatype==='number'">
        <v-text-field v-model="textValue" @change="onChange" type="number"></v-text-field>
    </div>
</div>
</template>

<script>
export default {
    props: ["condition", "fieldname"],
    components: {
        
    },
    data() {
        return {
            textValue: null
        }
    },
    created() {
        this.textValue = this.condition[this.fieldname]
    },
    methods: {
        onChange() {
            this.condition[this.fieldname] = this.textValue
        }
    }
}
</script>

<style>

</style>

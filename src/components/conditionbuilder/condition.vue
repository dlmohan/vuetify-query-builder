<template>
<v-card class="rule-container d-flex flex-column pa-2" flat>
    <v-card class="rule-field d-flex flex-row pa-2" outlined tile>
        <v-card class="rule-filter pa-2" flat>
            <v-select v-model="variableSelected" :name="variables" :items="variables" @change="chooseVariable"></v-select>
        </v-card>
        <v-card class="rule-operator pa-2" flat>
            <v-select name="stringOperators" v-model="rule.condition.operator" :items="stringOperators" v-if="stringType(rule)"></v-select>
            <v-select name="numericOperators" v-model="rule.condition.operator" :items="numericOperators" v-if="numberType(rule)"></v-select>
            <v-select name="dateOperators" v-model="rule.condition.operator" :items="dateOperators" v-if="dateType(rule)"></v-select>
        </v-card>
        <v-card class="rule-value pa-2" flat v-if="showConditionValue">
            <conditionvalue :condition="rule.condition" fieldname="value" />
        </v-card>
        <v-card class="rule-value pa-4" flat >
            <v-tooltip top>
            <template v-slot:activator="{ on }">
                <v-btn class="mx-2" fab dark small color="grey lighten-1" icon v-on="on" @click="removeSingleCondition(rule.condition.conditionname)">
                    <v-icon>mdi-close</v-icon>
                </v-btn>
            </template>
            <span>Delete</span>
        </v-tooltip>
        </v-card>
        
    </v-card>
</v-card>
</template>

<script>
import conditionvalue from "./conditionvalue";
export default {
    props: ["rule"],
    components: {
        conditionvalue
    },
    computed: {
        showConditionValue() {
            return this.rule.condition.operator !== 'isBlank' && this.rule.condition.operator !== 'isNotBlank' && this.rule.condition.datatype !== 'date';
        }
    },
    created() {
        this.initializeVariableDropDown()
        let variable = this.variableslist.find(v => v.name === this.rule.condition.name);
        if (variable) {
            this.variableSelected = variable.name
        }
    },
    data() {
        return {
            variables: [],
            variableSelected: null,
            variableslist: [{
                    "name": "name",
                    "datatype": "string"
                },
                {
                    "name": "age",
                    "datatype": "number"
                },
                {
                    "name": "dob",
                    "datatype": "date"
                }
            ],
            stringOperators: [{
                text: 'Starts With',
                value: 'startsWith'
            }, {
                text: 'Ends With',
                value: 'endsWith'
            }, {
                text: 'Contains',
                value: 'contains'
            }, {
                text: 'Equal',
                value: '=='
            }, {
                text: 'Not Equal',
                value: '!='
            }, {
                text: 'In',
                value: 'in'
            }, {
                text: 'Not In',
                value: 'notIn'
            }, {
                text: 'Is Blank',
                value: 'isBlank'
            }, {
                text: 'Is Not Blank',
                value: 'isNotBlank'
            }],
            numericOperators: [{
                    text: 'Equal',
                    value: '=='
                },
                {
                    text: 'Not Equal',
                    value: '!='
                },
                {
                    text: 'Greter Than',
                    value: '>'
                },
                {
                    text: 'Less Than',
                    value: '<'
                },
                {
                    text: 'Less Than Or Equal',
                    value: '<='
                },
                {
                    text: 'Greter Than Or Equal',
                    value: '>='
                }
            ],
            dateOperators: [{
                    text: 'Is Today',
                    value: 'isToday'
                },
                {
                    text: 'Is Yesterday',
                    value: 'isYesterday'
                },
                {
                    text: 'Is Last Week',
                    value: 'isLastWeek'
                },
                {
                    text: 'Is Last Month',
                    value: 'isLastMonth'
                },
                {
                    text: 'Is Last Year',
                    value: 'isLastYear'
                },
                {
                    text: 'Is Next Week',
                    value: 'isNextWeek'
                },
                {
                    text: 'Is Next Month',
                    value: 'isNextMonth'
                },
                {
                    text: 'Is Next Year',
                    value: 'isNextYear'
                }
            ]
        }
    },
    methods: {
        initializeVariableDropDown() {
            this.variables = this.variableslist.map(v => {
                return {
                    "text": v.name,
                    "value": v.name
                }
            })
        },
        chooseVariable(conditionName) {
            console.log('chooseVariable ' + conditionName)
            this.rule.condition.name = conditionName
            this.rule.condition.datatype = this.variableslist.find(v => v.name === conditionName).datatype
        },
        removeSingleCondition(conditionname) {
            this.$emit('remove-single-condition', conditionname);
        },
        stringType(rule) {
            return rule.condition.datatype === 'string';
        },
        numberType(rule) {
            return rule.condition.datatype === 'number';
        },
        dateType(rule) {
            return rule.condition.datatype === 'date';
        }
    }
}
</script>

<style>
</style>

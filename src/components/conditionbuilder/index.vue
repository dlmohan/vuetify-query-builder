<template>
<div style="width:100%" class="textareaadvanced">
    <v-textarea :label="label" v-model="conditionExpression" :rows="rows">
        <template slot="label">
            {{label}}
        </template>
        <template v-slot:append>
            <v-icon small color="cyan darken-2" @click="executeAppendIconAction">mdi-script</v-icon>
        </template>
    </v-textarea>
    <v-dialog v-model="showConditionBuilder" fullscreen hide-overlay transition="dialog-bottom-transition">
        <v-card>
            <v-toolbar dark color="primary lighten-1" fixed>
                <v-btn icon dark @click="showConditionBuilder = false">
                    <v-icon>mdi-close</v-icon>
                </v-btn>
                <v-toolbar-title>Condition Builder</v-toolbar-title>
                <v-spacer></v-spacer>
                <v-toolbar-items>
                    <v-btn dark text @click.native.stop="applyCondition()">Ok</v-btn>
                </v-toolbar-items>
            </v-toolbar>
            <v-card-text class="mt-3">
                {{conditionExpressionDisplay}}
                <conditionnode :ruleset="ruleset" @add-group="addGroup" @remove-group="removeGroup" @add-single-condition="addSingleCondition" @remove-single-condition="removeSingleCondition" @clause-changed="clauseChanged" />
            </v-card-text>
        </v-card>
    </v-dialog>

</div>
</template>

<script>
import conditionnode from "./conditionnode";
var uuid = require("uuid");
export default {
    props: ["parentobject", "fieldname", "label", "rows"],
    components: {
        conditionnode
    },
    computed: {
        conditionExpressionDisplay() {
            let conditionExpressionDisplay = this.addClause(this.ruleset)
            console.log(`conditionExpressionDisplay ${conditionExpressionDisplay}`)
            return conditionExpressionDisplay;
        }
    },
    data() {
        return {
            showConditionBuilder: false,
            conditionExpression: null,
            ruleset: {
                "rules": [{
                    "condition": {
                        "conditiontype": "group",
                        "conditionname": "group1",
                        "clause": 0,
                        "rules": [
                            //     {
                            //     "condition": {
                            //         "conditionname": "condition1",
                            //         "name": "age",
                            //         "scope": "user",
                            //         "operator": "equal",
                            //         "value": "lalit",
                            //         "datatype": "string",
                            //         "conditiontype": "single"
                            //     }
                            // }, {
                            //     "condition": {
                            //         "conditionname": "condition2",
                            //         "name": "salary",
                            //         "scope": "conversation",
                            //         "operator": "contains",
                            //         "value": "Nar",
                            //         "datatype": "string",
                            //         "conditiontype": "single"
                            //     }

                            // }, {
                            //     "condition": {
                            //         "conditiontype": "group",
                            //         "conditionname": "group2",
                            //         "clause": 1,
                            //         "rules": [{
                            //             "condition": {
                            //                 "conditionname": "condition3",
                            //                 "name": "personis",
                            //                 "scope": "dialog",
                            //                 "operator": "equal",
                            //                 "value": "kamala",
                            //                 "datatype": "string",
                            //                 "conditiontype": "single"
                            //             }

                            //         }]
                            //     }

                            // }
                        ]
                    }
                }]

            }

        }
    },
    created() {
        if (this.parentobject) {
            this.conditionExpression = this.parentobject[this.fieldname]
            if (this.parentobject['ruleset']) {
                this.ruleset = this.parentobject['ruleset']
            }
        }
        console.log(`condition builder created`)
    },
    methods: {
        executeAppendIconAction() {
            this.showConditionBuilder = true;
        },
        addClause(rule, clause) {
            let conditionExpressionDisplay = '';
            let rules = rule.rules;
            if (rules) {
                for (var i = 0; i < rules.length; i++) {
                    let _rule = rules[i];
                    let condition = _rule.condition;
                    let isLastElement = (i == rules.length - 1);
                    if (condition.conditiontype === 'group') {
                        let _clause = condition.clause === 0 ? ' and ' : ' or ';
                        conditionExpressionDisplay += `(${this.addClause(condition,_clause)})`
                    } else {
                        conditionExpressionDisplay += ` ${this.getConditionExpressionForVariable(condition)} ${clause && !isLastElement ?clause:''} `
                        console.log(`conditionExpressionDisplay1 ${conditionExpressionDisplay}`)
                    }
                }
            }
            return conditionExpressionDisplay;
        },
        getConditionExpressionForVariable(condition) {
            switch (condition.operator) {
                case 'startsWith':
                case 'endsWith':
                case 'contains': {
                    return `${condition.operator}(${condition.name},'${condition.value}')` //startsWith(name,'lalit')
                }
                case 'isBlank':
                case 'isNotBlank': {
                    return `${condition.operator}(${condition.name})` //isBlank(name)
                }
            }
            switch (condition.datatype) {
                case 'date': {
                    return `${condition.operator}(${condition.name})` //isToday(date)
                }
            }
            return `${condition.name} ${condition.operator} ${this.getConditionvalue(condition)}`;
        },
        getConditionvalue(condition) {
            if (condition.operator === 'isNull') {
                return '==null';
            }
            if (condition.operator === 'isNotNull') {
                return '!=null';
            }
            if (condition.datatype === 'string') {
                return `'${condition.value}'`
            }
            return condition.value
        },
        addGroup(groupName) {
            console.log('addGroup event to groupName ' + groupName)
            this.groupRecurse(this.ruleset, groupName, 'add', 'group')
        },
        removeGroup(groupName) {
            console.log('removeGroup event to groupName ' + groupName)
            this.groupRecurse(this.ruleset, groupName, 'remove', 'group')
        },
        addSingleCondition(groupName) {
            console.log('addSingleCondition event to groupName ' + groupName)
            this.groupRecurse(this.ruleset, groupName, 'add', 'single')
        },
        removeSingleCondition(conditionName) {
            console.log('removeSingleCondition event to conditionName ' + conditionName)
            this.groupRecurse(this.ruleset, conditionName, 'remove', 'single')
        },
        clauseChanged(details) {
            console.log('clauseChanged event to conditionName ' + details.conditionName + ' clause ' + details.clause)
        },
        applyCondition() {
            this.parentobject['ruleset'] = this.ruleset;
            this.showConditionBuilder = false;
            this.conditionExpression = this.conditionExpressionDisplay;
            this.parentobject[this.fieldname] = this.conditionExpression;
        },
        groupRecurse(ruleset, conditionName, action, conditionType) {
            let foundGroup = false;
            for (let i = 0; i < ruleset.rules.length; i++) {
                let rule = ruleset.rules[i];
                if (rule && rule.condition.conditionname === conditionName) {
                    action === 'add' ? this.addCondition(rule, conditionType) : this.removeCondition(ruleset, i, conditionType);
                    foundGroup = true;
                    break;
                } else {
                    if (rule && rule.condition.conditiontype === 'group') {
                        this.groupRecurse(rule.condition, conditionName, action, conditionType)
                    }
                }
            }
        },
        addCondition(rule, conditionType) {
            if (conditionType === 'group') {
                rule.condition.rules.push({
                    "condition": {
                        "conditiontype": "group",
                        "conditionname": "group_" + uuid(),
                        "clause": 0,
                        "rules": []
                    }
                })
            } else if (conditionType === 'single') {
                rule.condition.rules.push({
                    "condition": {
                        "conditionname": "condition_" + uuid(),
                        "name": null,
                        "scope": null,
                        "operator": null,
                        "value": null,
                        "datatype": null,
                        "conditiontype": "single"
                    }
                })
            }

        },
        removeCondition(ruleset, index, conditionType) {
            ruleset.rules.splice(index, 1);
        }
    }
}
</script>

<style>
/**This is to put the append icon on the textarea instead of on side */
.textareaadvanced .v-text-field .v-input__append-inner {
    position: absolute;
    right: 0;
}
</style>

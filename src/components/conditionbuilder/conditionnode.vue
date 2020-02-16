<template>
<v-container fluid v-if="ruleset.rules && ruleset.rules.length">
    <template v-for="rule in ruleset.rules">
        <v-card class="group-container d-flex flex-column" color="grey lighten-2" flat tile v-if="rule.condition.conditiontype==='group'" :key="JSON.stringify(rule)">
            <v-card class="group-header pa-2" outlined tile>
                <v-btn-toggle v-model="rule.condition.clause" mandatory @change="clauseChanged({conditionName:rule.condition.conditionname,clause:rule.condition.clause})">
                    <v-btn color="primary">
                        AND
                    </v-btn>
                    <v-btn color="primary">
                        OR
                    </v-btn>
                </v-btn-toggle>
                <v-menu transition="slide-y-transition" bottom>
                    <template v-slot:activator="{ on }">
                        <v-btn icon fab small color="grey lighten-1" v-on="on">
                            <v-icon>mdi-plus</v-icon>
                        </v-btn>
                    </template>
                    <v-list flat>
                        <v-list-item-group color="primary">
                            <v-list-item @click.prevent="addGroup(rule.condition.conditionname)">
                                <v-list-item-icon>
                                    <v-icon>mdi-plus</v-icon>
                                </v-list-item-icon>
                                <v-list-item-content>
                                    <v-list-item-title>Add Group</v-list-item-title>
                                </v-list-item-content>
                            </v-list-item>
                            <v-list-item @click.prevent="addSingleCondition(rule.condition.conditionname)">
                                <v-list-item-icon>
                                    <v-icon>mdi-plus</v-icon>
                                </v-list-item-icon>
                                <v-list-item-content>
                                    <v-list-item-title>Add Condition</v-list-item-title>
                                </v-list-item-content>
                            </v-list-item>
                        </v-list-item-group>
                    </v-list>
                </v-menu>
                 <v-tooltip top>
                    <template v-slot:activator="{ on }">
                        <v-btn class="mx-2" fab dark small color="grey lighten-1" icon v-on="on"  @click="removeGroup(rule.condition.conditionname)" v-if="rule.condition.conditionname!=='group1'">
                            <v-icon>mdi-close</v-icon>
                        </v-btn>
                    </template>
                    <span>Delete</span>
                </v-tooltip>
            </v-card>
            <v-card class="group-body pb-1" outlined tile>
                <v-card class="rule-list pb-1" flat>
                    <conditionnode :ruleset="rule.condition" 
                     @add-group="addGroup" @remove-group="removeGroup" @add-single-condition="addSingleCondition" @remove-single-condition="removeSingleCondition" @clause-changed="clauseChanged"/>
                </v-card>
            </v-card>
        </v-card>
            <condition :rule="rule" :key="JSON.stringify(rule)" v-else 
          @remove-single-condition="removeSingleCondition"/>
    </template>
</v-container>
</template>

<script>
import condition from "./condition";
export default {
    name: "conditionnode",
    props: ["ruleset"],
    components: {
        condition
    },
    methods: {
        addGroup(toGroup) {
            this.$emit('add-group', toGroup);
        },
        removeGroup(groupName) {
            this.$emit('remove-group', groupName);
        },
        addSingleCondition(toGroup) {
            this.$emit('add-single-condition', toGroup);
        },
        removeSingleCondition(conditionName) {
            this.$emit('remove-single-condition', conditionName);
        },
        clauseChanged(details) {
            this.$emit('clause-changed', details);
        }
    }
}
</script>

<style>

</style>

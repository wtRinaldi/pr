<template>
    <div class="tableContainer">
        <div class="row" style="background-color: lightcoral;">
            <div class="td td-large can-grow" @click="setSort('requirementName')">Name</div>
            <div class="td" @click="setSort('status')">Status</div>
            <div class="td" @click="setSort('dueDate')">Due Date</div>
            <div class="td td-large can-grow-4" @click="setSort('instructions')">Instructions</div>
            <div class="td td-small">Links</div>
        </div>
        <div class="row" v-if="checklistItems.length === 0">No Records Found</div>
        <div v-for="(item, itemIndex) in displayData" :key="itemIndex">
            <div class="row">
                <div class="td td-large can-grow">{{ item.requirementName }}</div>
                <div class="td">{{ item.status }}</div>
                <div class="td">{{ item.dueDate }}</div>
                <div class="td td-large can-grow-4">{{ item.instructions }}</div>
                <div class="td td-small"></div>
            </div>
        </div>
    </div>
</template>
<script>
    import sortData from 'cb-building-blocks/src/mixins/sort'
    export default {
        name: 'ChecklistTable',
        props: {
            checklistItems: {
                type: Array,
                default: () => []
            }
        },
        data () {
            return {
                sortBy: 'requirementName',
                isReversed: false
            }
        },
        methods: {
            setSort(type) {
                this.sortBy = type
                if(type === type) {
                    this.isReversed = !this.isReversed
                }
            }
        },
        computed: {
            displayData () {
                let formattedData = this.sortData([...this.checklistItems], this.sortBy, this.isReversed)
                return formattedData
            }
        },
        mixins: [sortData]
    }
</script>

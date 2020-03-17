<template>
    <div class="tableContainer">
        <div class="row" style="background-color: lightyellow;">
            <div class="td td-large can-grow" @click="setSort('name')">Name</div>
            <div class="td" @click="setSort('requirementName')">Checklist Item</div>
            <div class="td" @click="setSort('group')">Group</div>
            <div class="td" @click="setSort('status')">Status</div>
            <div class="td td-large can-grow-4" @click="setSort('instructions')">Instructions</div>
            <div class="td-small">ACTIONS</div>
        </div>
        <div class="row" v-if="checklistSearchResult.length === 0">No Records Found</div>
        <div v-for="(item, itemIndex) in displayData" :key="itemIndex">
            <div class="row">
                <div class="td td-large can-grow">{{ item.name }}</div>
                <div class="td">{{ item.requirementName }}</div>
                <div class="td">{{ item.group }}</div>
                <div class="td">{{ item.status }}</div>
                <div class="td td-large can-grow-4">{{ item.instructions }}</div>
                <div class="td td-small">icons</div>
            </div>
        </div>
    </div>
</template>
<script>
    import sortData from 'cb-building-blocks/src/mixins/sort'
    export default {
        name: 'memberSearchResultTable',
        props: {
            checklistSearchResult: {
                type: Array,
                default: () => []
            }
        },
        data () {
            return {
                sortBy: 'name',
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
                let formattedData = this.sortData([...this.checklistSearchResult], this.sortBy, this.isReversed)
                return formattedData
            }
        },
        mixins: [sortData]
    }
</script>

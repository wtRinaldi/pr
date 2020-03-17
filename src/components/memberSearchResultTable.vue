<template>
    <div class="tableContainer">
        <div class="row" style="background-color: lightyellow;">
            <div class="td td-large" @click="setSort('statusValue')">Status</div>
            <div class="td td-large can-grow" @click="setSort('name')">Name</div>
            <div class="td td-large can-grow" @click="setSort('group')">Group</div>
            <div class="td td-large" @click="setSort('role')">Role</div>
        </div>
        <div class="row" v-if="memberSearchResult.length === 0">No Records Found</div>
        <div v-for="(item, itemIndex) in displayData" :key="itemIndex">
            <div class="row">
                <div class="td td-large">
                    <status-bar :is-empty="item.summary.checklistItems.total === 0"
                        :complete-percentage="getPercentage(item.summary.checklistItems.complete, item.summary.checklistItems.total)"
                        :incomplete-percentage="getPercentage(item.summary.checklistItems.incomplete, item.summary.checklistItems.total)"
                        :pending-percentage="getPercentage(item.summary.checklistItems.pending, item.summary.checklistItems.total)"/>
                </div>
                <div class="td td-large can-grow">{{ item.name }}</div>
                <div class="td td-large can-grow">{{ item.group }}</div>
                <div class="td td-large">{{ item.role }}</div>
            </div>
        </div>
    </div>
</template>
<script>
    import statusBar from "./statusBar"
    import sortData from 'cb-building-blocks/src/mixins/sort'
    export default {
        name: 'memberSearchResultTable',
        components: {
          statusBar
        },
        props: {
            memberSearchResult: {
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
            getPercentage(num, total) {
                if (total > 0) {
                    return Math.round((num / total) * 100).toString()
                }
            },
            setSort(type) {
                this.sortBy = type
                if(type === type) {
                    this.isReversed = !this.isReversed
                }
            },
            formatStatus(data) {
                data.map(obj => {
                    if (obj.summary.checklistItems.total === 0) {
                        obj.statusValue = -1000000
                    } else {
                        obj.statusValue = (obj.summary.checklistItems.complete * 2) - (obj.summary.checklistItems.incomplete * 2) - obj.summary.checklistItems.pending
                    }
                })
                return data
            }
        },
        computed: {
            displayData () {
                let formattedData = [...this.memberSearchResult]
                formattedData = this.formatStatus(formattedData)
                formattedData = this.sortData(formattedData, this.sortBy, this.isReversed)
                return formattedData
            }
        },
        mixins: [sortData]
    }
</script>

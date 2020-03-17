<template>
    <div class="tableContainer">
        <div class="row" style="background-color: lightblue;">
            <div class="td">Status</div>
            <div class="td td-large can-grow" @click="setSort('member')">Name</div>
            <div class="td can-grow" @click="setSort('role')">Role</div>
        </div>
        <div v-for="(member, memberIndex) in displayData" :key="memberIndex">
            <div class="row" @click="toggleChecklistItemsTable(memberIndex)">
                <div class="td">{{ member.summary.checklistItems.total }}</div>
                <div class="td td-large can-grow">{{ member.member }}</div>
                <div class="td can-grow">{{ member.role }}</div>
            </div>
            <div class="nestedTable" v-if="checklistItemsTableIndex === memberIndex" style="padding: 1rem">
                <checklist-items-table :checklist-items="member.checklistItems"/>
            </div>
        </div>
    </div>
</template>
<script>
    import checklistItemsTable from "./checklistItemsTable";
    import memberships from '../data/memberships'
    import sortData from 'cb-building-blocks/src/mixins/sort'

    export default {
        name: 'MemberTable',
        components: {
            checklistItemsTable
        },
        data() {
            return {
                memberships: [],
                checklistItemsTableIndex: '',
                sortBy: '',
                isReversed: false
            }
        },
        methods: {
            toggleChecklistItemsTable (index) {
                this.checklistItemsTableIndex = (this.checklistItemsTableIndex === '' || this.checklistItemsTableIndex !== index) ? index : ''
            },
            setSort(type) {
                this.checklistItemsTableIndex = ''
                this.sortBy = type
                if(type === type) {
                    this.isReversed = !this.isReversed
                }
            }
        },
        computed: {
            displayData () {
                let formattedData = this.sortData([...this.memberships], this.sortBy, this.isReversed)
                return formattedData
            }
        },
        created() {
            // axios placeholder for memberships request
            this.memberships = memberships[0]
        },
        mixins: [sortData]
    }
</script>

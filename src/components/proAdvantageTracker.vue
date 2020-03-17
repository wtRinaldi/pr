<template>
    <div class="tableContainer">
        <div class="row">
            <cb-select label="Search Field" v-model="selectedSearchField" :key-values="searchTypeKeyValues"></cb-select>
            <cb-input label="Search" v-model="searchText"></cb-input>
            <cb-select label="Group By" v-if="isGroupDisplay" v-model="selectedGroupBy" :key-values="groupByKeyValues"></cb-select>
        </div>
        <div v-if="selectedSearchField === 'memberName'">
            <member-search-result-table :member-search-result="memberSearchResult" :search-text="searchText"/>
        </div>
        <div v-else-if="selectedSearchField === 'checklistItemName'">
            <checklist-item-name-search-result-table :checklist-search-result="checklistSearchResult" :search-text="searchText"/>
        </div>
        <div v-for="(groups, groupsIndex) in displayData" :key="groupsIndex" v-else>
            <div v-if="selectedGroupBy !== 'all'" style="padding: .5rem; background-color: turquoise;">{{ selectedGroupBy.toUpperCase() + ' - ' + groups[0][selectedGroupBy] }}</div>
            <div class="row" style="background-color: lightgreen;">
                <div class="td td-large can-grow" @click.self="setSort('name', groupsIndex)">Name</div>
                <div class="td">Checklist Status</div>
                <div class="td">Memberships Pending</div>
                <div class="td" @click="setSort('rotationStartDate', groupsIndex)">Rotation Start Date</div>
                <div class="td td-large can-grow" @click="setSort('school', groupsIndex)">School</div>
                <div class="td td-large can-grow" @click="setSort('site', groupsIndex)">Sites</div>
                <div class="td">links</div>
            </div>

            <div v-for="(group, groupIndex) in groups" :key="groupIndex">
                <div class="row" @click="toggleMemberTable(groupIndex)">
                    <div class="td td-large can-grow">{{ group.name }}</div>
                    <div class="td">{{ group.summary.checklistItems.total }}</div>
                    <div class="td">{{ group.summary.memberships.invited - group.summary.memberships.accepted }}</div>
                    <div class="td">{{ group.rotationStartDate }}</div>
                    <div class="td td-large can-grow">{{ group.school }}</div>
                    <div class="td td-large can-grow">{{ group.site }}</div>
                    <div class="td">links</div>
                </div>
                <div class="nestedTable" v-if="memberTableIndex === groupIndex" style="padding: 1rem">
                    <members-table/>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    /* eslint-disable */
    import groups from '../data/group-summary'
    import memberSearchResults from '../data/memberSearchResult'
    import checklistsSearchResults from '../data/checklistsSearch'

    import CbSelect from 'cb-building-blocks/src/components/CbSelect'
    import CbInput from 'cb-building-blocks/src/components/CbInput'
    import sortBy from 'cb-building-blocks/src/mixins/sort'
    import membersTable from './membersTable'
    import memberSearchResultTable from './memberSearchResultTable'
    import checklistItemNameSearchResultTable from "./checklistItemNameSearchResultTable";

export default {
    components: {
      membersTable,
        memberSearchResultTable,
        checklistItemNameSearchResultTable,
        CbSelect,
        CbInput
    },
    data () {
        return {
            groups: groups,
            memberTableIndex: '',
            selectedGroupBy: 'all',
            groupByKeyValues: [{'All': 'all'}, {'Term': 'term'}, {'Course': 'course'}, {'School': 'school'}],
            searchTypeKeyValues: [{'Group Name': 'name'}, {'Checklist Item Name': 'checklistItemName'}, {'Member Name': 'memberName'}, {'Rotation Start Date': 'rotationStartDate'}, {'School Name': 'school'}, {'Facility Name': 'site'}, {'Tag': 'tag'}],
            sortGroupIndex: 0,
            sortBy: 'name',
            isReversed: false,
            openTableIndex: '',
            searchText: '',
            selectedSearchField: 'name',
            memberSearchResult: memberSearchResults,
            checklistSearchResult: checklistsSearchResults
        }
    },
    methods: {
        toggleMemberTable(index) {
            this.memberTableIndex = (this.memberTableIndex === '' || this.memberTableIndex !== index) ? index : ''
        },
        filterGroupData(data) {
            let filteredData = []

            this.searchText.toLowerCase()
            if (this.searchText !== '') {
                let tempArr = [[]],
                    i

                for (i = 0; i < data.length; i++) {
                    if (data[i][this.selectedSearchField].toLowerCase().indexOf(this.searchText) > -1) {
                        tempArr[0].push(data[i])
                    }
                }
                filteredData = tempArr
            }
            return filteredData
        },
        groupData(data) {
            let groupedData = []
            let groupValues = data.map(group => group[this.selectedGroupBy])
            groupValues = [...new Set(groupValues)]
            groupValues.forEach(() => groupedData.push([]))
            data.forEach(group => {
                for (let i = 0; i < groupValues.length; i++) {
                    if (group[this.selectedGroupBy] === groupValues[i]) {
                        groupedData[i].push(group)
                        break
                    }
                }
            })
            return groupedData
        },
        setSort(type, groupsIndex) {
            this.sortGroupIndex = groupsIndex
            this.toggleMemberTable(this.memberTableIndex)
            this.sortBy = type
            if(type === type) {
                this.isReversed = !this.isReversed
            }
        }
    },
    computed: {
        displayData() {
            let formattedData = [[...groups]]

            if (this.searchText.length > 0) {
                switch (this.selectedSearchField) {
                    case 'name':
                    case 'rotationStartDate':
                    case 'school':
                    case 'site':
                    case 'tag':
                        formattedData = this.filterGroupData(formattedData[0])
                        break
                    case 'checklistItemName':
                        // filterChecklistData
                        // call group-summary?filter[requirementName]=this.searchText
                        break
                    case 'memberName':
                        // filter memberData
                        // call group-summary?filter[member]=this.searchText
                        break
                }
            }

            if (this.selectedGroupBy !== 'all') {
                formattedData = this.groupData(formattedData[0])
            }

            if (this.selectedGroupBy === 'all' && formattedData.length > 0) {
                formattedData[0] = this.sortData(formattedData[0], this.sortBy, this.isReversed)
            } else if (formattedData[this.sortGroupIndex] && formattedData[this.sortGroupIndex].length > 0) {
                formattedData[this.sortGroupIndex] = this.sortData(formattedData[this.sortGroupIndex], this.sortBy, this.isReversed)
            }

            return formattedData
        },
        isGroupDisplay() {
            if (this.selectedSearchField === 'checklistItemName' || this.selectedSearchField === 'memberName') {
                return false
            } else {
                return true
            }
        }
    },
    mixins: [sortBy]
}
</script>

<style>
    .tableContainer {
        display: flex;
        flex-direction: column;
    }
    .row {
        display: flex;
        flex-direction: row;
    }
    .td {
        flex: 0 0 150px;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
    }
    .td:hover {

    }
    .can-grow {
        flex-grow: 1;
    }
    .can-grow-4 {
        flex-grow: 4;
    }
    .td-large {
        flex-basis: 300px;
    }
    .td-small {
        flex-basis: 100px;
    }
    .nestedTable {
        flex: 1 1 100%;
    }
</style>

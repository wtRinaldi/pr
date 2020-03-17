<template>
    <div style="display: flex; width: 10rem;">
        <div v-if="isEmpty" :style="[baseStyle, noItemsStyle]"></div>
        <div v-else style="display: flex; width: 100%; height: 1rem;">
            <div :style="completedStyle"></div>
            <div :style="incompleteStyle"></div>
            <div :style="pendingStyle"></div>
        </div>
    </div>
</template>
<script>
    export default {
        name: 'statusBar',
        props: {

            isEmpty: {
                type: Boolean,
                default: true
            },
            completePercentage: {
                type: String,
                default: '0'
            },
            incompletePercentage: {
                type: String,
                default: '0'
            },
            pendingPercentage: {
                type: String,
                default: '0'
            }
        },
        data() {
            return {
                baseStyle: {
                    display: 'flex',
                    flexGrow: '1',
                    flexShrink: '1',
                    height: '1rem'
                },
                completedStyle: {
                    // backgroundColor: '#1ccb6f'
                    backgroundColor: 'green',
                    width: this.getCP + '%'
                },
                incompleteStyle: {
                    backgroundColor: '#f0696a',
                    width: this.getIP + '%'
                },
                pendingStyle: {
                    backgroundColor: '#fad024',
                    width: this.getPP + '%'
                },
                noItemsStyle: {
                    backgroundColor: 'lightgrey',
                    flexBasis: '1',
                },
                cp: '',
                ip: '',
                pp: ''
            }
        },
        computed: {
            getCP() {
                return this.completePercentage
            },
            getIP() {
                return this.incompletePercentage
            },
            getPP() {
                return this.pendingPercentage
            }
        }
    }
</script>

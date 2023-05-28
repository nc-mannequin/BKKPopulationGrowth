<script>
export default {
    name: 'Graph',
    props: ['data', 'min', 'max'],
    methods: {
        getBarStyles(value, min, max) {
            const isPositive = (value >= 0);
            const isMaxgtMin = (Math.abs(max) > Math.abs(min));
            let width = '0vw'
            let lowest = '0vw'
            if (isMaxgtMin) {
                width = (Math.abs(value)/Math.abs(max)) * 25 + 'vw'
                lowest = (Math.abs(min)/Math.abs(max)) * 25 + 'vw'
            }
            else {
                width = (Math.abs(value)/Math.abs(min)) * 25 + 'vw'
                lowest = (Math.abs(min)/Math.abs(min)) * 25 + 'vw'
            }

            if (min >= 0) {
                lowest = '0vw'
            }
            return {
                width: width,
                right: isPositive ? '': width,
                margin: '0vw 0vw 0vw ' + lowest,
            };
        },
    },
}
</script>

<template>

    <table>
        <tr>
            <th></th>
            <th>
                <span style="float: left;"> {{ min }}% </span>
                <span v-if="max <= 0"> 0% </span>
                <span v-if="max > 0" style="float: right;"> {{ max }}% </span>
            </th>
        </tr>
        <tr v-for="item in data">
            <th>{{ Object.keys(item)[0] }}</th>
            <th><div :style="getBarStyles(Object.values(item)[0], min, max)" style="float: center;" class="bar"></div></th>
        </tr>
    </table>

</template>

<style>
.bar {
    height: 1em;
    background-color: #ED2E7C;
    position: relative;
}
</style>
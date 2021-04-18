<template>

<div class="grid">

    <div class="grid__bloc">

        <ul class="grid__head">

            <li 
                class="grid__unit"
                v-for="(month, index) in fullMonthData"
                :key="index"
                >
                {{month}}
            </li>

        </ul>

        <ul class="grid__body">

            <li 
                class="grid__unit tooltip"
                v-for="(commit, date) in fullCommitData"
                :data-legend="loadTooltip(commit, date)"
                :data-date="date"
                :data-commit="commit"
                :key="date"
                :class="colorRange(commit)"
                >
            </li>

        </ul>

        <div class="grid__legend">

            <ul>
                <li class="grid__unit grid__unit--commit_0"></li>
                <li class="grid__unit grid__unit--commit_1"></li>
                <li class="grid__unit grid__unit--commit_2"></li>
                <li class="grid__unit grid__unit--commit_3"></li>
                <li class="grid__unit grid__unit--commit_4"></li>
            </ul>

            <p>
                Issues, merge requests, pushes, and comments.
            </p>

        </div>   

    </div>

    <ul class="grid__side">

        <li 
            class="grid__unit"
            v-for="(day, index) in days"
            :key="index"
            >
            {{day}}
        </li>

    </ul>
</div>
</template>

<script>
// import commitUnit from '@/components/commitUnit.vue'
import commitData from '@/assets/data.json'

export default {
    name: "commitGrid",
    // components: { commitUnit },
    data () {
        return {
            months: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'],
            days: ["M", "", "W", "", "F"],
            dayInMs: 86400000,
            jsonCommitData: commitData,
            fullCommitData: {},
            fullMonthData : []
        }
    },
    methods: {
        loadFullCommitData () {
            let i;
            // eslint-disable-next-line no-unused-vars
            let firstDay = this.getLastSunday(this.jsonCommitData);
            // eslint-disable-next-line no-unused-vars
            let lastDay = this.getNextSaturday(this.jsonCommitData);
            for (i = firstDay; i <= lastDay; i += this.dayInMs) {
                // eslint-disable-next-line no-unused-vars
                let j = this.formatDate(i);
                if (!(this.formatDate(i) in this.jsonCommitData)) { // if date doesn't already exist
                    this.formatDate(i) < this.getOldestCommitDate(this.jsonCommitData) || this.formatDate(i) > this.getLatestCommitDate(this.jsonCommitData) ?
                        this.fullCommitData[j] = -1 :
                        this.fullCommitData[j] = 0;
                } else { // if date already exist
                    this.fullCommitData[j] = this.jsonCommitData[j]; 
                }
            }
            this.loadFullMonthData();
        },
        loadFullMonthData () {
            let firstSunday = this.getLastSunday(this.jsonCommitData);
            let firstCommitDate = this.getOldestCommitDate(this.jsonCommitData)
            let lastSunday = this.unformatDate(this.getNextSaturday(this.jsonCommitData)) - this.dayInMs * 6;

            for (let i = firstSunday; i <= lastSunday; i += this.dayInMs * 7) {
                const j = new Date(i)
                if (j.getDate() <= 7) {
                    this.fullMonthData.push(this.months[j.getMonth()])
                } else {
                    this.fullMonthData.push("")
                }
            }

            if (firstSunday != firstCommitDate) { // aesthetic : avoid month above an empty unit
                this.fullMonthData.unshift("")
                this.fullMonthData.splice(2,1)
            }
        },
        loadTooltip (commit, date) {
            const d = new Date(date)
            return (commit <= 0 ? 'No' : commit) + " contribution" + (commit == 1 ? '' : 's') + " on " + this.months[d.getMonth()] + " " + d.getDate() + ", " + d.getFullYear()
        },
        formatDate(date) {
            let d = new Date(date),
                month = '' + (d.getMonth() + 1),
                day = '' + d.getDate(),
                year = d.getFullYear();
            if (month.length < 2) 
                month = '0' + month;
            if (day.length < 2) 
                day = '0' + day;
            return [year, month, day].join('-');
        },
        unformatDate(date) {
            let d = new Date(date),
                timestamp = d.getTime();
            return timestamp;
        },
        getOldestCommitDate(date) {
            return Object.keys(date).reduce((r, o) => o < r ? o : r)
        },
        getLatestCommitDate(date) {
            return Object.keys(date).reduce((r, o) => o > r ? o : r)
        },
        getLastSunday(date) {
            date = this.getOldestCommitDate(date);
            let d = new Date(date),
                lastSunday = d.setDate(d.getDate()-d.getDay());
            return lastSunday;
        },
        getNextSaturday(date) {
            date = this.getLatestCommitDate(date);
            let d = new Date(date),
                nextSaturday = d.setDate(d.getDate()+d.getDay(6));
            return nextSaturday;
        },
        colorRange(nbCommit) {
            switch (true) {
                case nbCommit >= 30 :
                    return 'grid__unit--commit_4';
                case nbCommit < 30 && nbCommit >= 20 :
                    return 'grid__unit--commit_3';
                case nbCommit < 20 && nbCommit >= 10 :
                    return 'grid__unit--commit_2';
                case nbCommit < 10 && nbCommit > 0 :
                    return 'grid__unit--commit_1';
                case nbCommit == 0 :
                    return 'grid__unit--commit_0';
                case nbCommit < 0 :
                    return 'grid__unit--commit_5';
            }
        }
    },
    beforeMount() {
        this.loadFullCommitData()
    }
}
</script>

<style lang="sass">
$commitColor_0: #ededed // 0
$commitColor_1: #acd6f2 // 1 to 9
$commitColor_2: #7fa8c9 // 10 to 19
$commitColor_3: #527ba0 // 20 to 29
$commitColor_4: #264e77 // 30 to ...

$gap: 2px
$unit: 11px
$fontsize: 8.75px

ul
    padding: 0
    margin: 0

.grid
    margin: auto
    font-size: $fontsize
    width: calc(53 * (#{$unit} + #{$gap}) - #{$gap}) // 53 columns and gap minus one exterior gap
    display: flex
    flex-direction: row-reverse
    justify-content: center
    &__bloc
        margin: 0
    &__head
        margin-bottom: 0
        display: grid
        grid-gap: $gap
        grid-auto-flow: column
        grid-template: repeat(53, 1fr)
    &__body
        margin-top: calc(#{$gap} * 1.5)
        display: grid
        grid-gap: $gap
        grid-auto-flow: column
        grid-template: repeat(7, 1fr) / repeat(53, 1fr)
    &__unit
        list-style: none
        background-color: none
        width: $unit
        height: $unit
        &--commit_0
            background-color: $commitColor_0
        &--commit_1
            background-color: $commitColor_1
        &--commit_2
            background-color: $commitColor_2
        &--commit_3
            background-color: $commitColor_3
        &--commit_4
            background-color: $commitColor_4
        &--commit-5
            background-color: none
    &__legend
        display: flex
        flex-direction: row
        justify-content: space-between
        margin-top: calc(#{$unit} + #{$gap})
        ul
            display: grid
            grid-gap: $gap
            grid-auto-flow: column
        p
            margin: 0
            font-weight: bold
            font-size: $fontsize
            color: #696969
    &__side
        vertical-align: middle
        margin-top: calc( ( #{$unit} * 2 ) + #{$gap} + #{$gap} * 1.5)
        li
            margin-bottom: $gap

.tooltip
    white-space: pre-wrap
    position: relative
    &:hover:after // tooltip
        background: #333
        background: rgba(0,0,0,.8)
        border-radius: 5px
        bottom: 15px
        color: #fff
        font-weight: bold
        content: attr(data-legend)
        left: -70px
        padding: 5px 7px
        position: absolute
        z-index: 98
        width: 140px
    &:hover:before // arrow below
        border: solid
        border-color: #333 transparent
        border-width: 6px 6px 0 6px
        bottom: 10px
        content: ""
        position: absolute
        z-index: 99
    &[data-commit="-1"]:hover::before
        content: none
    &:not([data-commit]):hover::before
        content: none
    &[data-commit="-1"]:hover::after
        content: none
    &:tooltip:not([data-commit]):hover::after
        content: none
</style>
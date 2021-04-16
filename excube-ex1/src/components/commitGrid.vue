<template>

<div class="grid">
    <ul class="grid__body">

        <li 
            class="grid__unit"
            v-for="commit in fullCommitData"
            :key="commit"
            :value="commit"
            :class="colorRange(commit)"
            >
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
            dayInMs: 86400000,
            firstDayInMs: 1562457600000, // deduced from picture : a monday 1st of july, there's one in 2019, so it probably begin the 30 june 2019 ***** NO > one week later, it begin on the 7th of july
            lastDayInMs: 1594512000000, // deduced from picture : a saturday in the first week of july, one year later, in 2020, must be the 4 july 2020 ***** NO > one week later, so 13th of july
            jsonCommitData: commitData,
            fullCommitData: {}
        }
    },
    methods: {
        loadFullCommitData () {
            let i;
            for (i = this.firstDayInMs; i < this.lastDayInMs; i += this.dayInMs) {
                // eslint-disable-next-line no-unused-vars
                let j = this.formatDate(i);
                if (!(this.formatDate(i) in this.jsonCommitData)) { // if date doesn't already exist
                    this.fullCommitData[j] = 0;
                } else { // if date already exist
                    this.fullCommitData[j] = this.jsonCommitData[j]; 
                }
            }
            console.log(this.fullCommitData)
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
        colorRange(nbCommit) {
            switch (true) {
                case nbCommit >= 30 :
                    return 'grid__unit--commitColor_4';
                case nbCommit < 30 && nbCommit >= 20 :
                    return 'grid__unit--commitColor_3';
                case nbCommit < 20 && nbCommit >= 10 :
                    return 'grid__unit--commitColor_2';
                case nbCommit < 10 && nbCommit > 0 :
                    return 'grid__unit--commitColor_1';
                case nbCommit == 0 :
                    return 'grid__unit--commitColor_0';
                case nbCommit < 0 :
                    return 'grid__unit--commitColor_5';
                    
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

.grid
    margin: auto
    width: 687px // 51 columns * ( 2px of gap, 11px of square ) = 53 * 13 px = 689px AND minus one exterior gap, so 689-2px= 687px
    &__body
        display: grid
        grid-gap: 2px
        grid-auto-flow: column
        grid-template: repeat(7, 1fr) / repeat(53, 1fr)
    &__unit
        list-style: none
        background-color: none
        width: 11px
        height: 11px
        &--commitColor_0
            background-color: $commitColor_0
        &--commitColor_1
            background-color: $commitColor_1
        &--commitColor_2
            background-color: $commitColor_2
        &--commitColor_3
            background-color: $commitColor_3
        &--commitColor_4
            background-color: $commitColor_4
        &--commitColor-5
            background-color: none
</style>
<template>

    <commitUnit />
    
</template>

<script>
import commitUnit from '@/components/commitUnit.vue'

export default {
    name: "commitGrid",
    components: { commitUnit },
    data () {
        return {
            dayInMs: 86400000,
            firstDayInMs: 1561899435000 // deduced from picture : a monday 1st of july, there's one in 2019, so it probably begin the 30 june 2019
        }
    },
}
</script>

<style lang="sass">

</style>
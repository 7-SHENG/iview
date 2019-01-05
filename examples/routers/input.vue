<template>
    <div style="width: 500px;margin: 100px;">
        {{ value6 }}
        <Input v-model="value5" type="textarea" placeholder="Enter something..." :max-length-of-row="34" :split-arr="splitArr" :max-length-special-row="maxLengthOneRow"></Input>
        <Input v-model="value6" type="textarea" :rows="4" placeholder="Enter something..."></Input>
    </div>
</template>
<script>
    export default {
        data () {
            return {
                value5: '',
                value6: '',
                splitArr: [" ", ",", ":", "+", "?", "'"],
                maxLengthOneRow: {
                    5: 33,
                    6: 33
                },
                maxLengthOneRow1: {
                    3: 30,
                    4: 31,
                    5: 32,
                    6: 33,
                    7: 34
                }
            }
        }
    }
</script>

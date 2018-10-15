<template>
    <div style="width: 500px;margin: 100px;">
        {{ value6 }}
        <Input v-model="value5" type="textarea" placeholder="Enter something..." :max-length-of-row="35" :split-arr="splitArr"></Input>
        <Input v-model="value6" type="textarea" :rows="4" placeholder="Enter something..."></Input>
    </div>
</template>
<script>
    export default {
        data () {
            return {
                value5: '',
                value6: '',
                splitArr: [' ', ',']
            }
        }
    }
</script>

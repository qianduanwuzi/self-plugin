<template>
    <self-select :opts="sOpts" :widths="50"  v-model="scoreNum" class="numDown" @change="changeHandler"></self-select>
</template>

<script>
export default {
    data() {
        return {
               sOpts: [
                    {key: 1, value: 1},
                    {key: 2, value: 2},
                    {key: 3, value: 3}],
                           scoreNum: 1,
        }
    }
}
</script>


<template>
    <div  class="aaa">

            <div class="list-group-item"  >
              {{config}}
            </div>

    </div>
</template>

<script>

export default {
    props: {
        config: {
            type: String,
            required: true
        }
    },
    created() {

        // this.itemList = this.getColumnConf(this.widgetSetting, this.config.key);
    },
    components: {
    },
    methods: {
    }
};
</script>

<style lang="scss" rel="stylesheet/scss">

</style>

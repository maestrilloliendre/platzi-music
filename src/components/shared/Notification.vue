<template lang="pug">
  .container
    .columns
        .column.is-5.is-offset-4
            .notification.is-success(v-if="success")
                slot(name="body")
            .notification.is-danger(v-else)
                slot(name="body")
         
</template>

<script>
    export default {
      props: {
        success: { type: Boolean, required: true }
      }
    }
</script>

<style lang="scss" scoped>
    .notification {
        margin: 10px;
    }
</style>

<template>
    <!-- #ifdef APP-NVUE -->
    <cell>
        <slot />
    </cell>
    <!-- #endif -->
    <!-- #ifndef APP-NVUE -->
    <slot />
    <!-- #endif -->
</template>

<script>
    export default {
        name: 'f-cell',
        props: {}
    }
</script>

<style lang="scss">
:deep(f-cell) {
    display: flex;
    flex-direction: column;
}
</style>

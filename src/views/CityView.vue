<template>
    <div>
        <Suspense>
            <async-city-view></async-city-view>
            <template #fallback>
                <p>Loading...</p>
            </template>
        </Suspense>
    </div>
</template>

<script setup>
    import AsyncCityView from '../components/AsyncCityView.vue';
</script>

<style lang="scss" scoped>

</style>
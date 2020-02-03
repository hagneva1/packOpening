<template>
    <div>
        <img :alt="card.set + card.color + card.name" v-for="card in images" :key="card" :src="card.pathLong">
    </div>
</template>

<script>
    export default {
        name: 'card',
        data() {
            return {
                images: [],
            };
        },

        mounted() {
            this.importAll(require.context('../assets/Images/', true, /\.png$/));
        },

        methods: {
            importAll(r) {
                r.keys().forEach(key => (this.images.push({ pathLong: r(key), pathShort: key,
                    set: key.split('/')[1], color: key.split('/')[2], name: key.split('/')[3]
                })));
            },
        },
    };
</script>

<style scoped>

</style>
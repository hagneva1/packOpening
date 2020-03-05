<template>
    <div>
        <img class="booster" v-for="booster in boosters" :key="booster" :src="booster.pathLong"
             :alt="booster.set + booster.color + booster.name" @click="getCardPack(booster.set)">
        <img @click="showCard(card.id)" :class="'card'+ dos.length +' ' + card.class" :alt="card.set + card.color + card.name" v-for="card in dos" :key="card" :src="card.pathLong">
    </div>
</template>

<script>
    export default {
        name: 'card',
        selectItem: null,
        legendaryCpt: 0,
        data() {
            return {
                images: [],
                pack: [],
                dos: []
            };
        },
        computed: {
            boosters: function () {
                return this.images.filter(function (image) {
                    if (image.color === 'booster.png') {
                        return image
                    }
                })
            },

            back: function () {
                return this.images.filter(function (image) {
                    if (image.color === 'dos.png') {
                        return image
                    }
                })
            },
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

            getCardPack(set) {
                this.pack = []
                this.dos = []
                let booster = this.selectBooster(set);
                let rarity = this.randomRarity()

                for(let i=0; i<rarity.length; i++) {
                    let color = rarity[i]

                    let oneBack = Object.assign({}, this.back.find(b => b.set == set));
                    oneBack['class'] = color;
                    oneBack['id'] = i;
                    this.dos.push(oneBack);

                    let oneColorBooster = this.selectColor(booster, color)
                    let oneCard = oneColorBooster[Math.floor(Math.random() * oneColorBooster.length)]
                    oneCard['class'] = color;

                    this.pack.push(oneCard);
                }

                this.legendaryCpt += 1
            },

            randomRarity() {
                let rarity = {};
                rarity.base = 40;
                rarity.common = 30;
                rarity.rare = 20;
                rarity.epic = 9;
                rarity.legendary = 1;
                let pool = [];
                let boosterRarity = [];
                let nbCard = 5;

                // Third rule : Add 10% chance to have one more card
                if (Math.floor(Math.random() * 2) == 0) {
                    nbCard += 1;

                }

                // Construction of pool of weight to certified probability
                for (let i in rarity) {
                    for (let j = 0; j < rarity[i] ; j++) {
                        pool.push(i)
                    }
                }

                for (let k = 0; k < nbCard; k++) {
                    boosterRarity.push(pool[Math.floor(Math.random() * pool.length)])
                }

                // First rule : Protection against full base and common card -> certified at least one rare card
                if (!boosterRarity.includes("rare") && !boosterRarity.includes("epic") && !boosterRarity.includes("legendary")) {
                    boosterRarity[Math.floor(Math.random() * boosterRarity.length)] = "rare"
                }

                // Second rule : Ensure one legendary card every 20 packs.
                if (this.legendaryCpt == 20 && !boosterRarity.includes("legendary")) {
                    boosterRarity[Math.floor(Math.random() * boosterRarity.length)] = "legendary"
                }
                if (boosterRarity.includes("legendary")){
                    this.legendaryCpt = 0
                }

                return boosterRarity
            },

            selectBooster(set) {
                return this.images.filter(function (image) {
                    if (image.set === set && image.color != "booster.png" && image.color != "dos.png") {
                        return image
                    }
                })
            },

            selectColor(booster, color) {
                return booster.filter(function (image) {
                    if (image.color === color) {
                        return image
                    }
                })
            },

            showCard(id) {
                this.dos.splice(id, 1, this.pack[id]);
            }
        },
    };
</script>

<style scoped>
    .card5 {
        width: 15%;
        margin: 2%;
    }

    .card6 {
          width: 12%;
          margin: 2%;
      }

    .legendary:hover {
        -webkit-filter: drop-shadow(0 0 1em darkgoldenrod);
        filter: drop-shadow(0 0 1em darkgoldenrod);
    }

    .epic:hover {
        -webkit-filter: drop-shadow(0 0 1em orchid);
        filter: drop-shadow(0 0 1em orchid);
    }

    .rare:hover {
        -webkit-filter: drop-shadow(0 0 1em cornflowerblue);
        filter: drop-shadow(0 0 1em cornflowerblue);
    }

    .common:hover {
        -webkit-filter: drop-shadow(0 0 1em white);
        filter: drop-shadow(0 0 1em white);
    }

    .base:hover {
        -webkit-filter: drop-shadow(0 0 1em grey);
        filter: drop-shadow(0 0 1em grey);
    }

    .booster {
        width: 20%;
        margin: 2%;
        padding: 0;
    }
</style>
<template>
    <div style="width: 100%;">
        <div class="card__item">
            <div class="tabs">
                <div class="tab">
                    <button :class=" tab === 'add' ? 'active': ''" @click="tab = 'add'">Add</button>
                </div>
                <div class="tab">
                    <button :class=" tab === 'remove' ? 'active': ''" @click="tab = 'remove'">Remove
                    </button>
                </div>
            </div>
        </div>
        <div style="width: 100%;" v-if=" tab === 'add'">
            <CurrencyField title="Input" :coin="coinOne" :dataList="dataList" @change="changeOne" v-model="valueOne"/>
            <q-btn color="secondary" unelevated round icon="add" size="lg" class="sync-btn"></q-btn>
            <CurrencyField title="Input" :coin="coinTwo" :dataList="dataList" @change="changeTwo"
                           v-model="valueTwo"/>
            <div class="flex justify-between bg-secondary q-my-sm q-pa-sm items-center rounded-borders"
                 v-if="coinOne.value && coinTwo.value">
                <div>
                    <div>
                        0.345 {{coinOne.value}} per {{coinTwo.value}}
                    </div>
                    <div>
                        2.345 {{coinTwo.value}} per {{coinOne.value}}
                    </div>
                </div>
                <div class="text-right">
                    <div>&lt; 0.01%</div>
                    <div>Share of Pool</div>
                </div>
            </div>
            <button class="action">Connect to wallet</button>
        </div>
        <div style="width: 100%;" v-else>
            <InputField title="Amount to Remove" v-model="removeValue"></InputField>
            <q-btn color="secondary" unelevated round icon="arrow_downward" size="lg" class="sync-btn"></q-btn>
            <div class="flex items-center justify-between bg-secondary rounded-borders q-pa-md">
                <div>
                    <div>You Will Recieve</div>
                    <div class="text-info"><small>Recieve WFTM</small></div>
                </div>
                <div class="flex">
                    <div class="flex bg-dark rounded-borders items-center q-ma-xs">
                        <div class="q-pa-sm">
                            <img :src="dataList[0].logo" width="35px">
                        </div>
                        <div class="q-pa-sm">
                            <div>-</div>
                            <div>{{dataList[0].value}}</div>
                        </div>
                    </div>
                    <div class="flex bg-dark rounded-borders items-center q-ma-xs">
                        <div class="q-pa-sm">
                            <img :src="dataList[1].logo" width="35px">
                        </div>
                        <div class="q-pa-sm">
                            <div>-</div>
                            <div>{{dataList[1].value}}</div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="flex">
                <div style="flex:1" class="q-pa-sm">
                    <button class="action">Approve</button>
                </div>
                <div style="flex:1" class="q-pa-sm">
                    <button class="action">Enter an Amount</button>
                </div>
            </div>
            <q-btn icon-right="chevron_right" size="md" class="q-my-md" @click="liqPos=true">
                View liquidity position
            </q-btn>
        </div>
        <q-dialog v-model="liqPos">
            <q-card style="width: 700px; max-width: 80vw;">
                <q-card-section class="row items-center q-pb-none">
                    <q-btn icon="chevron_left" flat round dense v-close-popup/>
                    <div>GO Back</div>
                </q-card-section>
                <q-card-section>
                    <div class="text-h5 q-mx-md">My Liquidity Position</div>
                </q-card-section>
                <q-card-section>
                    <q-banner dense inline-actions
                              class="text-white q-py-sm q-my-sm q-mx-sm flex rounded-borders"
                              v-if="liqBanner"
                              style="background: #0993ec60"
                    >
                        <div class="row">
                            <div class="col-11 text-h6 q-mb-sm">
                                Liquidity Provider Rewards
                            </div>
                            <div class="col-1 flex items-center justify-end">
                                <q-icon name="close" class="q-pa-xs cursor-pointer" @click="banner=false"></q-icon>
                            </div>
                        </div>
                        <div>
                            Lorem ipsum dolor sit amet, consectetur adipisicing elit. Adipisci at deleniti dolore
                            dolores ea, ex id illum minus modi molestias officiis possimus quo sed temporibus ullam
                            velit veniam. Atque et expedita non perspiciatis quae qui quis repellat, similique! A
                            aliquid commodi dolorum earum eius enim exercitationem explicabo
                        </div>
                    </q-banner>
                    <div class="flex items-center justify-center bg-black rounded-borders q-mx-sm"
                         style="min-height: 100px">
                        <div class="ma-auto">
                            No Liquidity found
                        </div>
                    </div>
                    <div class="flex">
                        <div style="flex:1" class="q-pa-sm">
                            <button class="action">Add</button>
                        </div>
                        <div style="flex:1" class="q-pa-sm">
                            <button class="action">Import</button>
                        </div>
                    </div>
                </q-card-section>

            </q-card>
        </q-dialog>
    </div>

</template>

<script>

    import CurrencyField from '@/components/currency/CurrencyField';
    import InputField from "./InputField";

    export default {
        components: {
            InputField,
            CurrencyField,
        },
        data() {
            return {
                dataList: [
                    {id: 1, value: 'FTM', name: 'Fantom', logo: require('@/assets/logo/ftm.png')},
                    {id: 2, value: 'BQB', name: 'BloqBall', logo: require('@/assets/logo/bqb.png')}
                ],
                liqPos: false,
                liqBanner: true,
                coinOne: {},
                coinTwo: {},
                valueOne: null,
                valueTwo: null,
                tab: 'add',
                removeValue: 0
            }
        },
        created() {
            this.coinOne = this.dataList[0]
        },
        methods: {
            changeOne(item) {
                if (this.coinTwo === item) {
                    var position = 'top-';
                    this.$q.notify({
                        message: 'Coins can not be the same to swap!',
                        color: 'red',
                        position,
                        timeout: 5000
                    })
                } else {
                    this.coinOne = item
                }

            },
            changeTwo(item) {
                if (this.coinOne === item) {
                    var position = 'top';
                    this.$q.notify({
                        message: 'Coins can not be the same to swap!',
                        color: 'red',
                        position,
                        timeout: 5000
                    })
                } else {
                    this.coinTwo = item
                }
            },
            swap() {
                let itemOne = this.coinOne;
                let valueOne = this.valueOne;
                this.coinOne = this.coinTwo;
                this.valueOne = this.valueTwo;
                this.coinTwo = itemOne;
                this.valueTwo = valueOne;
            }
        }

    };
</script>

<style scoped>
    .sync-btn {
        margin: -10px 0 -10px 10px;
        border: 3px solid var(--q-color-dark);
        z-index: 10;
    }

    .action {
        background: #0993ec33;
        width: 100%;
        margin-top: 15px;
        border: none;
        border-radius: 10px;
        padding: 15px;
        font-size: 1rem;
        color: #0993ec;
        cursor: pointer;
    }

    .action:hover {
        background-color: #0993ec60;
    }


    .card__item {
        display: flex;
        width: 100%;
        justify-content: space-between;
    }

    .card__item:first-child {
        padding-bottom: 15px;
    }


    .tabs {
        width: 100%;
        display: flex;
        border-radius: 10px;
        padding: 2px;
        background: var(--q-color-secondary)
    }

    .tab {
        flex: 1;
        /* display: inline; */
    }

    .tab > button {
        background: transparent;
        display: flex;
        justify-content: center;
        align-items: center;
        padding: 10px;
        border-radius: 7px;
        margin: 2px;
        width: 100%;
        color: #999;
        border: none;
        cursor: pointer;
    }

    .tab > .active {
        background-image: linear-gradient(to right, var(--q-color-primary), var(--q-color-info));
        color: #fff;
        font-weight: 600;
    }
</style>
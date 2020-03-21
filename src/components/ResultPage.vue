<template>
    <v-container>
        <v-row>
            <span class="font-weight-black">使用Smartalk + {{ results.planName }}的话，<br>每个月的话费 + 流量费用约为 :</span>
        </v-row>
        <v-row>
            <v-spacer/>
            <span class="display-2 font-weight-black">
                {{ totalCost }}円
            </span>
            <v-spacer/>
        </v-row>
        <v-progress-linear indeterminate color="cyan"/>


        <!-- 有的月份不打电话 -->
        <v-row v-if="results.isNoneCall">
            <span class="font-weight-black">若当月不打电话，每个月的话费 + 流量费用则约为 :</span>
        </v-row>
        <v-row v-if="results.isNoneCall">
            <v-spacer/>
            <span class="display-2 font-weight-black">
                {{ results.dataCost }}円
            </span>
            <v-spacer/>
        </v-row>

        <v-progress-linear v-if="results.isNoneCall" indeterminate color="cyan"/>

        <v-row>
            <span class="font-weight-black">与现在的话费和流量费相比，每个月将节约 :</span>
        </v-row>
        <v-row>
            <v-col cols="12">
                <span class="display-2 font-weight-black">
                    {{ results.currentCost - totalCost }}円
                </span>
                <v-progress-linear indeterminate color="cyan"/>
                <!-- 有的月份不打电话 -->
                <div v-if="results.isNoneCall">
                    <p class="display-3">～</p>
                    <span class="display-2 font-weight-black">
                        {{ results.currentCost - results.dataCost }}円
                    </span>
                    <v-progress-linear indeterminate color="cyan"/>
                </div>
            </v-col>
        </v-row>

        <!-- 节约换算 -->
        <v-alert
        v-for="item in items" :key="item.message"
        color="green"
        dark
        :icon="item.icon"
        transition="scale-transition"
        >
        {{ item.message }}
        </v-alert>
    </v-container>
</template>
<script>
export default {
    props:{
        results: Object
    },
    computed: {
        items() {
            let items = []
            const value = this.results.currentCost - (this.results.dataCost + this.results.phoneCost)
            if (this.hasMilkTee(value) !== 0) {
                items.push(this.hasMilkTee(value))
            }
            if (this.hasFastFoods(value) !== 0) {
                items.push(this.hasFastFoods(value))
            }
            if (this.hasKaraok(value) !== 0) {
                items.push(this.hasKaraok(value))
            }
            if (this.hasMovie(value) !== 0) {
                items.push(this.hasMovie(value))
            }
            if (this.hasBeer(value) !== 0) {
                items.push(this.hasBeer(value))
            }
            return items
        },
        totalCost() {
            return this.results.phoneCost + this.results.dataCost
        }
    },
    methods: {
        hasMilkTee(value) {
            if (value < 600) {
                return 0
            }
            const result = {
                icon: 'mdi-cup',
                message: 'タピオカ約' + Math.ceil(value / 600) + '杯'
            }
            return result
        },
        hasFastFoods(value) {
            if (value < 900) {
                return 0
            }
            const result = {
                icon: 'mdi-food',
                message: '外食約' + Math.ceil(value / 900) + '回'
            }
            return result
        },
        hasKaraok(value) {
            if (value < 1000) {
                return 0
            }
            const result = {
                icon: 'mdi-microphone',
                message: 'カラオケ約' + Math.ceil(value / 1000) + '回'
            }
            return result
        },
        hasMovie(value) {
            if (value < 1800) {
                return 0
            }
            const result = {
                icon: 'mdi-movie',
                message: '映画約' + Math.ceil(value / 1800) + '回'
            }
            return result
        },
        hasBeer(value) {
            if (value < 2000) {
                return 0
            }
            const result = {
                icon: 'mdi-beer',
                message: '飲み会約' + Math.ceil(value / 2000) + '回'
            }
            return result
        }
    },
}
</script>
<template>
    <v-container>
        <v-row>
            <span class="font-weight-black">{{ $t('userSmartalk') }} + {{ results.planName + $t('afterUserSmartalk') }}<br>
            {{ $t('smartalkResult') }}</span>
        </v-row>
        <v-row>
            <v-spacer/>
            <span class="display-2 font-weight-black">
                {{ totalCost }}円
            </span>
            <v-spacer/>
        </v-row>
        <v-progress-linear indeterminate color="cyan"/>

        <!-- 若当月不打电话 -->
        <span v-if="results.isNoneCall">
            <v-row>
                <span class="font-weight-black">{{ $t('noPhoneCost') }}</span>
            </v-row>
            <v-row>
                <v-spacer/>
                <span class="display-2 font-weight-black">
                    {{ results.dataCost }}円
                </span>
                <v-spacer/>
            </v-row>

            <v-progress-linear indeterminate color="cyan"/>
        </span>
        <!-- 与现在的相比 -->
        <span v-if="isMerit">
            <v-row>
                <span class="font-weight-black">{{ $t('saveCost') }}</span>
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
        </span>
        <span v-else>
            <v-alert
            color="red"
            dark
            icon="mdi-heart"
            transition="scale-transition"
            >
            {{ $t('congratulations') }}
            </v-alert>
        </span>
        <span v-if="isMerit">
            <!-- 节约换算 -->
            <p class="font-weight-black headline">{{ $t('exchange') }}</p>
            <v-alert
            v-for="item in items" :key="item.message"
            color="green"
            dark
            :icon="item.icon"
            transition="scale-transition"
            >
            {{ item.message }}
            </v-alert>
            <a :href="$t('blogUrl')" target="blank">{{ $t('checkBlog') }}</a>
        </span>
    </v-container>
</template>
<script>
export default {
    props:{
        results: Object
    },
    computed: {
        items() {
            if (!this.isMerit) { return }
            let items = []
            const value = this.results.isNoneCall ? this.results.currentCost - this.results.dataCost : this.results.currentCost - this.totalCost
            if (this.hasMilkTea(value) !== 0) {
                items.push(this.hasMilkTea(value))
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
            if (items.length === 0) {
                const nonsense = {
                    icon: 'mdi-clock',
                    message: this.$t('accumulative')
                }
                items.push(nonsense)
            }
            return items
        },
        totalCost() {
            return this.results.phoneCost + this.results.dataCost
        },
        isMerit() {
            let result = this.results.currentCost - (this.results.phoneCost + this.results.dataCost)
            if (result < 0) {
                if (this.results.isNoneCall) {
                    result = this.results.currentCost - this.results.dataCost >= 0 ? this.results.currentCost - this.results.dataCost : result
                }
            }
            if (result >= 0) {
                return true
            }
            return false
        },
        /**
         * 不打电话时有更划算 && 有不打电话的情况
         */
        showExchange() {
            if (this.results.currentCost - this.results.dataCost >= 0 && this.results.isNoneCall) {
                return true
            }
            return false
        }
    },
    methods: {
        hasMilkTea(value) {
            if (value < 600) {
                return 0
            }
            const result = {
                icon: 'mdi-cup',
                message: this.$t('milkTea') + '約'  + Math.ceil(value / 600) + '杯'
            }
            return result
        },
        hasFastFoods(value) {
            if (value < 900) {
                return 0
            }
            const result = {
                icon: 'mdi-food',
                message: this.$t('food') + '約' + Math.ceil(value / 900) + this.$t('times')
            }
            return result
        },
        hasKaraok(value) {
            if (value < 1000) {
                return 0
            }
            const result = {
                icon: 'mdi-microphone',
                message: this.$t('karaok') + '約'  + Math.ceil(value / 1000) + this.$t('times')
            }
            return result
        },
        hasMovie(value) {
            if (value < 1800) {
                return 0
            }
            const result = {
                icon: 'mdi-movie',
                message: this.$t('movie') + '約'  + Math.ceil(value / 1800) + this.$t('times')
            }
            return result
        },
        hasBeer(value) {
            if (value < 2000) {
                return 0
            }
            const result = {
                icon: 'mdi-beer',
                message: this.$t('beer') + '約'  + Math.ceil(value / 2000) + this.$t('times')
            }
            return result
        }
    },
}
</script>
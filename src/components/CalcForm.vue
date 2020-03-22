<template>
    <v-container>
         <v-alert type="error" v-if="showAlert">
            {{ $t("required") }}
        </v-alert>
        <!-- 流量 -->
        <v-row>
            <label>{{ $t('dataPlan') }}</label>
        </v-row>
        <v-row>
            <v-col cols="11">
                <v-select
                v-model="selectedPlan"
                :items="plans"
                :placeholder="$t('dataPlan')"
                ></v-select>
            </v-col>
        </v-row>
        <!-- 通话时长 -->
        <v-row>
            <label>{{ $t('callTime') }}</label>
        </v-row>
        <v-row>
            <v-col cols="9">
                <v-text-field type="number" :placeholder="$t('callTime')" v-model="callTime"/>
            </v-col>
            <v-col cols="2">
                <span class="display-2 font-weight-black">min</span>
            </v-col>
        </v-row>
        <!-- 是否有一通电话都不打的月份 -->
        <v-row>
            <label>{{ $t('frequency') }}</label>
        </v-row>
        <v-row>
            <v-radio-group v-model="isNoneCall" row>
                <v-radio :label="$t('hasNever')" value="1"></v-radio>
                <v-radio :label="$t('always')" value="0"></v-radio>
            </v-radio-group>
        </v-row>
        <!-- 是否有移动wifi -->
        <v-row>
            <v-col cols="11">
                <v-checkbox v-model="hasPoketWifi" :label="$t('hasPoketWifi')"></v-checkbox>
            </v-col>
            <v-col cols="11" v-if="hasPoketWifi">
                <v-text-field type="number" :placeholder="$t('poketWifiCost')" v-model="poketWifiCost"/>
            </v-col>
        </v-row>
        <!-- 现在每个月的话费 -->
        <v-row>
            <label>{{ $t('currentCost')}} </label>
        </v-row>
        <v-row>
            <v-col cols="9">
                <v-text-field type="number" :placeholder="$t('inputCurrentCostPlz')" v-model="phoneCost"/>
            </v-col>
            <v-col cols="2">
                <span class="display-2 font-weight-black">円</span>
            </v-col>
        </v-row>
        <v-row>
            <v-spacer />
            <v-btn @click="sendParams" large color="primary">{{ $t('calc') }}</v-btn>
            <v-spacer />
        </v-row>
    </v-container>
</template>
<script>
import Data from '../const/constData.js'

export default {
    data() {
        return {
            selectedPlan: '',
            callTime: '',
            phoneCost: '',
            isNoneCall: '1',
            hasPoketWifi: false,
            poketWifiCost: '',
            plans: Data.dataPlans, // 流量套餐在 constData.js 里统一管理
            showAlert: false
        }
    },
    methods: {
        /**
         * 确认必须参数全部都被输入后，将参数重新封装后传给父组件进行计算
         * 若有必要参数没有被输入则显示提示
         */
        sendParams() {
            if (!this.validate()) {
                this.showAlert = true
                return
            }
            this.setPlan()
            const resultList = {
                'dataPlan': this.selectedPlan,
                'callTime': this.callTime === '' ? 0 : this.callTime,
                'phoneCost': Number(this.phoneCost),
                'isNoneCall': this.isNoneCall === '1' ? true : false,
                'poketWifiCost': this.hasPoketWifi ? Number(this.poketWifiCost) : 0
            }
            this.$emit('calc', resultList)
        },
        /**
         * 检测必要参数是否输入
         */
        validate() {
            let hasValue = true
            if(!this.selectedPlan) { hasValue = false }
            if(!this.phoneCost) { hasValue = false }
            if(this.hasPoketWifi && !this.poketWifiCost) { hasValue = false }
            return hasValue
        },
        /**
         * 逆向查找流量套餐对象
         */
        setPlan() {
            this.plans.some((plan) => {
                if (plan.value === this.selectedPlan) {
                    this.selectedPlan = plan
                    return true
                }
            })
        }
    },
}
</script>
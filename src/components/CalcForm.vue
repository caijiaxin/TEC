<template>
    <v-container>
         <v-alert type="error" v-if="showAlert">
            请输入完整信息以便计算
        </v-alert>
        <!-- 流量 -->
        <v-row>
            <label>每月使用流量</label>
        </v-row>
        <v-row>
            <v-col cols="7">
                <v-select
                v-model="selectedPlan"
                :items="plans"
                placeholder="请选择每月使用流量"
                ></v-select>
            </v-col>
        </v-row>
        <!-- 通话时长 -->
        <v-row>
            <label>每月通话时长</label>
        </v-row>
        <v-row>
            <v-col cols="7">
                <v-text-field type="number" placeholder="请输入每月通话时长" v-model="callTime"/>
            </v-col>
            <v-col>
                <span class="display-2 font-weight-black">min</span>
            </v-col>
        </v-row>
        <!-- 是否有一通电话都不打的月份 -->
        <v-row>
            <label>每月电话使用情况</label>
        </v-row>
        <v-row>
            <v-radio-group v-model="isNoneCall" row>
                <v-radio label="有的时候一个月都不打一通电话" value="1"></v-radio>
                <v-radio label="每个月或多或少都会打电话" value="0"></v-radio>
            </v-radio-group>
        </v-row>
        <!-- 是否有移动wifi -->
        <v-row>
            <v-col cols="7">
                <v-checkbox v-model="hasPoketWifi" label="是否有移动wifi?"></v-checkbox>
            </v-col>
            <v-col cols="8" v-if="hasPoketWifi">
                <v-text-field type="number" placeholder="请输入每月移动wifi的价格" v-model="poketWifiCost"/>
            </v-col>
        </v-row>
        <!-- 现在每个月的话费 -->
        <v-row>
            <label>现在每月的话费</label>
        </v-row>
        <v-row>
            <v-col cols="7">
                <v-text-field type="number" placeholder="请输入现在每月的话费" v-model="phoneCost"/>
            </v-col>
            <v-col>
                <span class="display-2 font-weight-black">円</span>
            </v-col>
        </v-row>
        <v-row>
            <v-spacer />
            <v-btn @click="sendParams" large>计算</v-btn>
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
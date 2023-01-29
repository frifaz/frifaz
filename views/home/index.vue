<template>
    <el-row  class="home" :gutter="20">
      <el-col :span="8" style="margin-top: 20px">
        <el-card shadow="hover">
            <div class="user">
                <img :src="userImg" />
                <div class="userinfo">
                    <p class="name">Admin</p>
                    <p class="access">超级管理员</p>
                </div>
            </div>
            <div class="login-info">
                <p>上次登陆时间：<span>2022/11/19</span></p>
                <p>上次登陆地点：<span>襄阳</span></p>
            </div>
        </el-card>
        <el-card style="margin-top:20px; height:460px">
            <el-table :data="tabData">
                <el-table-column 
                    v-for="(val,key) in tabLabel"
                        :key='key'
                        :prop="key"
                        :label="val"  
                ></el-table-column>
            </el-table>
        </el-card>
      </el-col>
    <el-col :span="16" style="padding-left: 10px">
        <div class="num">
            <el-card v-for="item in countData" :key="item.name" :body-style="{ display: 'flex', padding: 0 }">
                <i class="icon" :class="`el-icon-${item.icon}`" :style="{ background: item.color }"></i>
                <div class="detail">
                    <p class="price">￥{{ item.value }}</p>
                    <p class="desc">{{ item.name }}</p>
                </div>
            </el-card>
        </div>
        <el-card style="height:280px">
            <div style="height:280px" ref="echarts"></div>
        </el-card>
        <div class="graph" style="margin-top:10px">
            <el-card class="child" style="height:260px">
                <div style="height:240px" ref="userEcharts"></div>
            </el-card>
            <el-card class="child" style="height:260px">
                <div style="height:240px" ref="videoEcharts"></div>
            </el-card>
        </div>
      </el-col>
    </el-row>
</template>

<script>
import {getData} from '../../api/data'
import * as echarts from 'echarts'
export default {
    name:'home',
    data() {
        return {
            userImg:require('../../src/assets/images/780.jpg'),
            tabData:[],
            tabLabel:{
                name:'课程',
                todayBuy:'今日购买',
                monthBuy:'月购买',
                totalBuy:'总购买'
            },
            countData: [
                {
                name: "今日支付订单",
                value: 1234,
                icon: "success",
                color: "#2ec7c9",
                },
                {
                name: "今日收藏订单",
                value: 210,
                icon: "star-on",
                color: "#ffb980",
                },
                {
                name: "今日未支付订单",
                value: 1234,
                icon: "s-goods",
                color: "#5ab1ef",
                },
                {
                name: "本月支付订单",
                value: 1234,
                icon: "success",
                color: "#2ec7c9",
                },
                {
                name: "本月收藏订单",
                value: 210,
                icon: "star-on",
                color: "#ffb980",
                },
                {
                name: "本月未支付订单",
                value: 1234,
                icon: "s-goods",
                color: "#5ab1ef",
                },
            ],
        }
    },
    created() {
        getData().then(res=>{
            const {code,data}=res.data
            //  console.log(res.data)
            if(code===20000){
                this.tabData=data.tabData
                const order=data.orderData
                const xData=order.data
                const keyArr=Object.keys(order.data[0])
                const series=[]
                keyArr.forEach(key=>{
                    series.push({
                        name:key,
                        data:order.data.map(item=>item[key]),
                        type:'line'
                    })
                })
                const option={
                    xAxis:{
                        data:xData
                    },
                    yAxis:{},
                    legend:{
                        data:keyArr
                    },
                    series
                }
                const E=echarts.init(this.$refs.echarts)
                E.setOption(option)

                //用户图
                const userOption={
                    legend: {
                        // 图例文字颜色
                        textStyle: {
                        color: "#333",
                        },
                    },
                    grid: {
                        left: "20%",
                    },
                    // 提示框
                    tooltip: {
                        trigger: "axis",
                    },
                    xAxis: {
                        type: "category", // 类目轴
                        data: data.userData.map(item=>item.date),
                        axisLine: {
                        lineStyle: {
                            color: "#17b3a3",
                        },
                        },
                        axisLabel: {
                        interval: 0,
                        color: "#333",
                        },
                    },
                    yAxis: [
                        {
                        type: "value",
                        axisLine: {
                            lineStyle: {
                            color: "#17b3a3",
                            },
                        },
                        },
                    ],
                    color: ["#2ec7c9", "#b6a2de"],
                    series: [
                        {
                            name:'新增用户',
                            data:data.userData.map(item=>item.new),
                            type:'bar'
                        },
                        {
                            name:'活跃用户',
                            data:data.userData.map(item=>item.active),
                            type:'bar'
                        }
                    ],
                }
                const U = echarts.init(this.$refs.userEcharts)
                U.setOption(userOption)

                //饼图
                const videoOption={
                    tooltip: {
                        trigger: "item",
                    },
                    color: [
                        "#0f78f4",
                        "#dd536b",
                        "#9462e5",
                        "#a6a6a6",
                        "#e1bb22",
                        "#39c362",
                        "#3ed1cf",
                    ],
                    series: [{
                        data:data.videoData,
                        type:'pie'
                    }
                    ],
                    }
                const V =echarts.init(this.$refs.videoEcharts)
                V.setOption(videoOption)
            }
        })
    },
}
</script>
<style lang='less' scoped>
    .num{
        padding-bottom: 10px;
        display: flex;
        flex-wrap: wrap-reverse;
        align-content: flex-start;
        margin-top: 20px;
    }
    .el-card.is-always-shadow, .el-card.is-hover-shadow:focus, .el-card.is-hover-shadow:hover{
    box-sizing: border-box;
    flex: 0 0 50%;
    }
    .graph{
        display: flex;
        flex-wrap: wrap-reverse;
        align-content: flex-start;
    }
</style>
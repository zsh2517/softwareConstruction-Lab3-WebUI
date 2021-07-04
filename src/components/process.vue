<template>
    <div>
        <!--        个进程的 ID、名称、最短执行时间、最长执行时间-->
        <el-row :gutter="0">
            <el-col :span="12">
                <el-table
                        :data="processData"
                        border
                        style="width: 100%; margin-bottom: 10px;">
                    <el-table-column
                            prop="pid"
                            label="PID"
                            width="50">
                    </el-table-column>
                    <el-table-column
                            prop="name"
                            label="进程名称"
                            width="100">
                    </el-table-column>
                    <el-table-column
                            prop="minRunTime"
                            label="最短执行"
                            width="100">
                    </el-table-column>
                    <el-table-column
                            prop="maxRunTime"
                            label="最长执行"
                            width="100">
                    </el-table-column>
                    <el-table-column
                            prop="cntRunTime"
                            label="已运行"
                            width="100">
                    </el-table-column>
                    <el-table-column
                            label="操作"
                            width="100">
                        <template slot-scope="scope">
                            <el-button @click="removeProcess(scope.row)" type="text" size="small">删除</el-button>
                            <span v-if="Number(scope.row.cntRunTime) < Number(scope.row.minRunTime)">
                                <el-button @click="runPid(scope.row)" type="text" size="small">运行</el-button>
                            </span>
                            <span v-if="Number(scope.row.cntRunTime) >= Number(scope.row.minRunTime)">
                                <span>已完成</span>
                            </span>
                        </template>
                    </el-table-column>
                </el-table>

                <el-form ref="form" :model="newProcess" label-width="80px">
                    <el-form-item label="进程 ID">
                        <el-input
                                v-model="newProcess.pid"
                                type="number"
                                placeholder="进程 ID...">
                        </el-input>
                    </el-form-item>
                    <el-form-item label="进程名称">
                        <el-input
                                v-model="newProcess.name"
                                placeholder="进程名称...">
                        </el-input>
                    </el-form-item>
                    <el-form-item label="最短执行">
                        <el-input
                                v-model="newProcess.minRunTime"
                                type="number"
                                placeholder="最短执行...">
                        </el-input>
                    </el-form-item>
                    <el-form-item label="最长执行">
                        <el-input
                                v-model="newProcess.maxRunTime"
                                type="number"
                                placeholder="最长执行...">
                        </el-input>
                    </el-form-item>

                    <div style="width: 100%; text-align: center;">
                        <el-button size="medium" type="primary" @click="addProcess">
                            添加
                        </el-button>
                    </div>
                </el-form>
            </el-col>
            <el-col :span="12">
                <el-table
                        :data="runningData"
                        border
                        style="width: 100%; margin-bottom: 10px;">
                    <el-table-column
                            prop="start"
                            label="开始"
                            width="50">
                    </el-table-column>
                    <el-table-column
                            prop="end"
                            label="结束"
                            width="50">
                    </el-table-column>
                    <el-table-column
                            prop="pid"
                            label="PID"
                            width="50">
                    </el-table-column>
                    <el-table-column
                            prop="name"
                            label="进程名称"
                            width="100">
                    </el-table-column>
                    <el-table-column
                            prop="minRunTime"
                            label="最短执行"
                            width="100">
                    </el-table-column>
                    <el-table-column
                            prop="maxRunTime"
                            label="最长执行"
                            width="100">
                    </el-table-column>
<!--                    <el-table-column-->
<!--                            prop="cntRunTime"-->
<!--                            label="已运行"-->
<!--                            width="100">-->
<!--                    </el-table-column>-->
                </el-table>
                <div style="width: 100%; text-align: center;">
                    <div>
                        空闲率: {{ (blank * 100.0).toFixed(2) }}%，当前执行 {{ tickCount }} 周期
                    </div>
                    <el-button size="medium" type="primary" @click="nextTick">
                        跳过 Tick（空闲）
                    </el-button>
                    <el-button size="medium" type="primary" @click="randRun">
                        随机运行
                    </el-button>
                    <el-button size="medium" type="primary" @click="maxRun">
                        选择最长时间运行
                    </el-button>
                </div>
            </el-col>
        </el-row>
    </div>
</template>

<script>
// import axios from 'axios';

import axios from "axios";

export default {
    name: "process",
    data() {
        return {
            newProcess: {
                pid: 1,
                name: "",
                minRunTime: 1,
                maxRunTime: 1
            },
            runningData: [
                {tick: 0, pid: 1, name: "process 1", minRunTime: 3, maxRunTime: 5, cntRunTime: 0}
            ],
            processData: [
                {pid: 1, name: "process 1", minRunTime: 3, maxRunTime: 5, cntRunTime: 0}
            ],
            inputRunTime: 1,
            tickCount: 0,
            blank: 0,
        }
    },
    methods: {
        updateAllInfo() {
            axios.post("http://127.0.0.1:8899/process/getAllInfo", {}).then(e => {
                let data = e.data.data;
                this.processData = data.data;
                this.runningData = data.intervals;
                console.log(data.intervals);
                data.intervals.sort((x, y) => {
                    return Number(x.start) - Number(y.start);
                });
                console.log(data.intervals);
                console.log(this.runningData);
                this.blank = Number(data.blank);
                this.tickCount = Number(data.tickCount);
                let maxpid = 0;
                this.processData.forEach(ele => {
                    maxpid = Math.max(maxpid, Number(ele.pid));
                })
                this.newProcess.pid = maxpid + 1;

            });
        },
        addProcess() {
            axios.post("http://127.0.0.1:8899/process/addProcess", {
                pid: Number(this.newProcess.pid),
                name: this.newProcess.name,
                minRunTime: Number(this.newProcess.minRunTime),
                maxRunTime: Number(this.newProcess.maxRunTime),
            }).then(e => {
                let data = e.data.data;
                if (data === true) {
                    alert("成功");
                } else {
                    alert("失败");
                }
                this.newProcess.pid = Number(this.newProcess.pid) + 1;
                this.updateAllInfo();
            });
        },
        removeProcess(row) {
            axios.post("http://127.0.0.1:8899/process/delProcess", {
                pid: Number(row.pid),
            }).then(e => {
                let data = e.data.data;
                if (data === true) {
                    alert("成功");
                } else {
                    alert("失败");
                }
                this.updateAllInfo();
            });
        },
        nextTick() {
            this.inputRunTime = Number(prompt("请输入运行时长", this.inputRunTime));
            if (this.inputRunTime > 0) {
                axios.post("http://127.0.0.1:8899/process/runFree", {
                    length: this.inputRunTime
                }).then(e => {
                    let data = e.data.data;
                    if (data === true) {
                        alert("成功");
                    } else {
                        alert("失败");
                    }
                    this.updateAllInfo();
                });
            }
        },
        randRun() {
            // this.inputRunTime = Number(prompt("请输入运行时长", this.inputRunTime));
            // if (this.inputRunTime > 0) {
            axios.post("http://127.0.0.1:8899/process/autoRunRandom", {
                // length: this.inputRunTime
            }).then(e => {
                let data = e.data.data;
                if (data !== null) {
                    alert(`运行进程 ${data} 1个时钟周期`);
                } else {
                    alert("运行失败，全部进程已完成");
                }
                this.updateAllInfo();
            });
            // }
        },
        maxRun() {
            axios.post("http://127.0.0.1:8899/process/autoRunPriority", {
                // length: this.inputRunTime
            }).then(e => {
                let data = e.data.data;
                if (data !== null) {
                    alert(`运行进程 ${data} 1个时钟周期`);
                } else {
                    alert("运行失败，全部进程已完成");
                }
                this.updateAllInfo();
            });
        },
        runPid(row) {
            this.inputRunTime = Number(prompt("请输入运行时长", this.inputRunTime));
            if (this.inputRunTime > 0) {
                axios.post("http://127.0.0.1:8899/process/runMax", {
                    pid: row.pid,
                    length: this.inputRunTime
                }).then(e => {
                    let data = e.data.data;
                    if (data === true) {
                        alert(`运行进程 ${row.pid}，长度 ${length} tick 成功`);
                    } else {
                        alert("失败");
                    }
                    this.updateAllInfo();
                });
            }
        }
    }, mounted() {
        this.updateAllInfo();
    }
}
</script>

<style scoped>

</style>

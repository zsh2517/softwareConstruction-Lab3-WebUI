<template>
    <el-row :gutter="0">
        <el-col :span="12">
            <el-calendar v-model="calendarData">
                <template
                        slot="dateCell"
                        slot-scope="{date, data}">
                    <!--                    <div v-if="data.isSelected">-->
                    <!--                        <div>-->
                    <!--                            <div v-if="dateStatus === 0" class="is-selected" style="color: red;">-->
                    <!--                                <div><b>{{ data.day.split('-').slice(1).join('-') }}</b></div>-->
                    <!--                                <div>超出范围</div>-->
                    <!--                            </div>-->
                    <!--                            <div v-if="dateStatus === 1" class="is-selected"-->
                    <!--                                 style="color: green;">-->
                    <!--                                <div><b>{{ data.day.split('-').slice(1).join('-') }}</b></div>-->
                    <!--                                <div>暂未安排</div>-->
                    <!--                            </div>-->
                    <!--                            <div v-if="dateStatus === 2 || typeof(dateStatus) === typeof('')" class="is-selected"-->
                    <!--                                 style="color: blue;">-->
                    <!--                                <div><b>{{ data.day.split('-').slice(1).join('-') }}</b></div>-->
                    <!--                                <div>{{ dateStatus }}</div>-->
                    <!--                            </div>-->
                    <!--                        </div>-->
                    <!--                    </div>-->
                    <!--                    <div v-if="!data.isSelected">-->
                    <div>
                        <div v-if="calendarDateStatus(date) === 0" class="is-selected" style="color: red;">
                            <div><b>{{ data.day.split('-').slice(1).join('-') }}</b></div>
                            <div>超出范围</div>
                        </div>
                        <div v-if="calendarDateStatus(date) === 1" class="is-selected"
                             style="color: green;">
                            <div><b>{{ data.day.split('-').slice(1).join('-') }}</b></div>
                            <div>暂未安排</div>
                        </div>
                        <div v-if="calendarDateStatus(date) === 2 || typeof(calendarDateStatus(date)) === typeof('')"
                             class="is-selected"
                             style="color: blue;">
                            <div><b>{{ data.day.split('-').slice(1).join('-') }}</b></div>
                            <div>{{ calendarDateStatus(date) }}</div>
                        </div>
                    </div>
                    <!--                    </div>-->
                </template>
            </el-calendar>
            <el-button type="primary" @click="showImport=true;">导入格式化文本</el-button>
            <el-dialog
                    title="粘贴导入的内容"
                    :visible.sync="showImport"
                    width="30%">
                <span>
                    <el-input
                            type="textarea"
                            :rows="10"
                            placeholder="请输入内容"
                            v-model="textarea">
                    </el-input>
                </span>
                <span slot="footer" class="dialog-footer">
                <el-button @click="showImport = false">取 消</el-button>
                <el-button type="primary" @click="showImport = false; importConfirm();">确 定</el-button>
                </span>
            </el-dialog>
            <div>空白率：{{ (blank * 100).toFixed(2) }} %</div>
        </el-col>
        <el-col :span="12" style="text-align: left; border-left: 1px rgba(0, 0, 0, 0.1) solid;">
            <el-form ref="form" :model="timeForm" label-width="80px">
                <el-form-item label="起始日期">
                    <el-date-picker
                            style="width: 100%"
                            v-model="timeForm.startTime"
                            value-format="yyyy-MM-dd"
                            type="date"
                            placeholder="选择日期">
<!--                            @change="setDate"-->
                    </el-date-picker>
                </el-form-item>
                <el-form-item label="结束日期">
                    <el-date-picker
                            style="width: 100%"
                            v-model="timeForm.endTime"
                            value-format="yyyy-MM-dd"
                            type="date"
                            placeholder="选择日期">
<!--                            @change="setDate"-->
                    </el-date-picker>
                </el-form-item>
                <el-form-item>
                    <div style="width: 100%; text-align: center;">
                        <div style="color: red;">后端每次启动需要重新确认修改时间后再进行操作</div>
                        <el-button size="medium" type="primary" @click="setDate">
                            确认修改时间（修改后将重置重新录入）
                        </el-button>
                    </div>
                </el-form-item>
            </el-form>
            <el-divider></el-divider>
            <el-form ref="form" :model="newUser" label-width="80px">
                <el-form-item label="姓名">
                    <el-input
                            v-model="newUser.name"
                            placeholder="姓名...">
                    </el-input>
                </el-form-item>
                <el-form-item label="职位">
                    <el-input
                            v-model="newUser.post"
                            placeholder="职位...">
                    </el-input>
                </el-form-item>
                <el-form-item label="电话">
                    <el-input
                            v-model="newUser.tel"
                            placeholder="电话...">
                    </el-input>
                </el-form-item>
                <div style="width: 100%; text-align: center;">
                    <el-button size="medium" type="primary" @click="employeeAdd">
                        添加
                    </el-button>
                </div>
            </el-form>
            <el-divider></el-divider>
            <el-table
                    :data="tableData"
                    border
                    style="width: 100%">
                <el-table-column
                        prop="name"
                        label="姓名"
                        width="100">
                </el-table-column>
                <el-table-column
                        prop="post"
                        label="职位"
                        width="100">
                </el-table-column>
                <el-table-column
                        prop="tel"
                        label="电话"
                        width="150">
                </el-table-column>
                <el-table-column
                        label="操作"
                        width="150">
                    <template slot-scope="scope">
                        <el-button @click="employeeRemove(scope.row)" type="text" size="small">删除</el-button>
                        <el-button @click="assignmentAdd(scope.row)" type="text" size="small">安排当前日期</el-button>
                    </template>
                </el-table-column>
            </el-table>
            <div style="width: 100%; text-align: center;">
                <el-button size="medium" type="primary" @click="resetAssignment">
                    重置
                </el-button>
                <el-button size="medium" type="primary" @click="autoAssignment">
                    自动排班
                </el-button>
            </div>
            <el-dialog
                    title="提示"
                    :visible.sync="dialogVisible"
                    width="30%">
                <span><el-form ref="form" :model="worker" label-width="80px">
                <el-form-item label="起始日期">
                    <el-date-picker
                            v-model="worker.startTime"
                            value-format="yyyy-MM-dd"
                            type="date"
                            placeholder="选择日期">
                    </el-date-picker>
                </el-form-item>
                <el-form-item label="结束日期">
                    <el-date-picker
                            v-model="worker.endTime"
                            value-format="yyyy-MM-dd"
                            type="date"
                            placeholder="选择日期">
                    </el-date-picker>
                </el-form-item>
            </el-form></span>
                <span slot="footer" class="dialog-footer">
                <el-button @click="dialogVisible = false">取 消</el-button>
                <el-button type="primary" @click="dialogVisible = false; ModelSuccess();">确 定</el-button>
                </span>
            </el-dialog>

        </el-col>
    </el-row>
</template>

<script>
/* eslint-disable no-debugger */
import axios from "axios";

Date.prototype.format = function (fmt) {
    var o = {
        "M+": this.getMonth() + 1,                 //月份
        "d+": this.getDate(),                    //日
        "h+": this.getHours(),                   //小时
        "m+": this.getMinutes(),                 //分
        "s+": this.getSeconds(),                 //秒
        "q+": Math.floor((this.getMonth() + 3) / 3), //季度
        "S": this.getMilliseconds()             //毫秒
    };
    if (/(y+)/.test(fmt)) {
        fmt = fmt.replace(RegExp.$1, (this.getFullYear() + "").substr(4 - RegExp.$1.length));
    }
    for (var k in o) {
        if (new RegExp("(" + k + ")").test(fmt)) {
            fmt = fmt.replace(RegExp.$1, (RegExp.$1.length == 1) ? (o[k]) : (("00" + o[k]).substr(("" + o[k]).length)));
        }
    }
    return fmt;
}

Date.prototype.fromStr = function (str) {
    var date = new Date(str);
    if (typeof (str) === typeof ("")) {
        if (/\d{4}-\d{2}-\d{2}/.test(str)) {
            return date;
        } else {
            date.setHours(0);
            date.setMinutes(0);
            date.setSeconds(0);
            date.setMilliseconds(0);
        }
    }
    return date;
}

Date.prototype.toStr = function () {
    return this.format("yyyy-MM-dd");
}

Date.prototype.between = function (start, end) {
    if (typeof (start) === typeof ("")) {
        // start = (new Date).toStr(start);
        start = new Date(start);
    }
    if (typeof (end) === typeof ("")) {
        // end = (new Date).toStr(end);
        end = new Date(end);
    }
    end = end.setDate(end.getDate() + 1);
    return this >= start && this < end;

}

export default {
    name: "duty",
    data() {
        return {
            timeForm: {
                startTime: "2021-07-01",
                endTime: "2021-07-01"
            }, worker: {
                startTime: "2021-07-01",
                endTime: "2021-07-01",
                name: ""
            },
            calendarData: new Date("2021-07-03"),
            tableData: [{
                name: "张三",
                post: "工程师",
                tel: "133-2333-3333"
            }],
            newUser: {
                name: "李四",
                post: "攻城狮",
                tel: "2333-3333"
            },
            data: {
                intervals: [
                    {start: 0, end: 2, value: {name: "张三", post: "工程师", tel: "133-2333-3333"}}
                ]
            },
            intervals: [],
            dialogVisible: false,
            blank: 0.0,
            showImport: false,
            textarea: ""

        }
    },
    computed: {
        dateStatus() {
            let start = this.timeForm.startTime;
            let end = this.timeForm.endTime;
            // eslint-disable-next-line vue/no-side-effects-in-computed-properties
            let chooseDate = new Date(this.calendarData);
            console.log(start, end, this.calendarData);
            if (!(chooseDate.between(start, end))) {
                return 0;
            }
            // debugger;
            let ret = 1;
            this.intervals.forEach(interval => {
                if (chooseDate.between(interval.start, interval.end)) {
                    ret = interval.label.name;
                }
            })
            // 0. 不可选择（超出时间范围）
            // 1. 可选，为空
            // 2. 非空
            // this.setDate();
            return ret;
        }
    },
    methods: {
        calendarDateStatus(date) {
            let start = this.timeForm.startTime;
            let end = this.timeForm.endTime;
            // eslint-disable-next-line vue/no-side-effects-in-computed-properties
            let chooseDate = new Date(date);
            if (!(chooseDate.between(start, end))) {
                return 0;
            }
            // debugger;
            let ret = 1;
            this.intervals.forEach(interval => {
                if (chooseDate.between(interval.start, interval.end)) {
                    ret = interval.label.name;
                }
            })
            // 0. 不可选择（超出时间范围）
            // 1. 可选，为空
            // 2. 非空
            // this.setDate();
            return ret;
        },
        employeeRemove(row) {
            console.log(row);
            axios.post("http://127.0.0.1:8899/duty/employeeRemove", {
                name: row.name
            }).then(e => {
                let data = e.data.data;
                if (data === true) {
                    alert("成功");

                } else {
                    alert("失败");
                }
                this.updateAllInfo();
            })
        },
        updateAllInfo() {
            // 更新包括员工信息和排班信息的东西
            let that = this;
            axios.post("http://127.0.0.1:8899/duty/getAllInfo").then(e => {
                let data = e.data.data;
                that.tableData = data.data;
                that.intervals = data.intervals;
                that.blank = data.blank;
                that.timeForm.startTime = new Date(data.start).toStr();
                that.calendarData = new Date(data.start);
                let temp = new Date(data.start);
                temp.setDate(temp.getDate() + data.length);
                that.timeForm.endTime = temp.toStr();
                let startTime = new Date(that.timeForm.startTime);
                for (let i = 0; i < that.intervals.length; i++) {
                    let temp = new Date(startTime);
                    temp.setDate(temp.getDate() + that.intervals[i].start);
                    that.intervals[i].start = temp.toStr();
                    temp = new Date(startTime);
                    temp.setDate(temp.getDate() + that.intervals[i].end);
                    that.intervals[i].end = temp.toStr();
                }
                console.log(that.intervals);
            })
        },
        setDate() {
            // debugger;
            let start = new Date(this.timeForm.startTime).toStr();
            let end = new Date(this.timeForm.endTime).toStr();
            axios.post("http://127.0.0.1:8899/duty/setDate", {
                "startDate": start,
                "endDate": end
            }).then(() => {
                this.resetAssignment();
            })
        },
        employeeAdd() {
            axios.post("http://127.0.0.1:8899/duty/employeeAdd", {
                name: this.newUser.name,
                post: this.newUser.post,
                tel: this.newUser.tel
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
        showModel() {
            this.worker.startTime = this.timeForm.startTime;
            this.worker.endTime = this.timeForm.endTime;
            this.dialogVisible = true;
        },
        ModelSuccess() {
            // debugger;
            let t = (x) => {
                return (new Date(x).getTime() - new Date(this.timeForm.startTime).getTime()) / 86400000
            };
            axios.post("http://127.0.0.1:8899/duty/insertConflict", {
                start: t(this.worker.startTime),
                end: t(this.worker.endTime)
            }).then(e => {
                let data = e.data.data;
                if (data === false) {
                    axios.post("http://127.0.0.1:8899/duty/assignmentAdd", {
                        name: this.worker.name,
                        start: t(this.worker.startTime),
                        end: t(this.worker.endTime)
                    }).then(e => {
                        let data = e.data.data;
                        if (data === true) {
                            alert("成功");
                        } else {
                            alert("失败");
                        }
                        this.updateAllInfo();
                    });
                } else {
                    alert("时间冲突");
                }
                this.updateAllInfo();
            })


        },
        assignmentAdd(row) {
            this.worker.name = row.name;
            this.showModel();
        },
        autoAssignment() {
            axios.post("http://127.0.0.1:8899/duty/autoAssignment").then(() => {
                this.updateAllInfo();
            })
        },
        resetAssignment() {
            axios.post("http://127.0.0.1:8899/duty/resetAssignment").then(() => {
                this.updateAllInfo();
            })
        },
        importConfirm() {
            axios.post("http://127.0.0.1:8899/duty/import", {
                content: this.textarea
            }).then(() => {
                this.updateAllInfo();
            })
        }
    },
    mounted: function () {
        this.updateAllInfo();
    }
}
</script>

<style scoped>

</style>

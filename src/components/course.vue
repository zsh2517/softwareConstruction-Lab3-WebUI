<template>
    <div>
        <div style="text-align: left;">
            <el-table
                    :data="scheduleData">
                <el-table-column
                        prop="time"
                        label="时间"
                        width="150">

                </el-table-column>
                <el-table-column
                        prop="mon"
                        :label="`星期一 (${weekStartDate.offset(0, cntWeek)})`"
                        width="150">
                    <template slot-scope="scope">
                        <div v-if="scope.row.mon.length > 0">
                            <div v-for="(item, index) in scope.row.mon" :key="index">
                                {{ item }}
                            </div>
                        </div>
                    </template>
                </el-table-column>
                <el-table-column
                        prop="tue"
                        :label="`星期二 (${weekStartDate.offset(1, cntWeek)})`"
                        width="150">
                    <template slot-scope="scope">
                        <div v-if="scope.row.tue.length > 0">
                            <div v-for="(item, index) in scope.row.tue" :key="index">
                                {{ item }}
                            </div>
                        </div>
                    </template>
                </el-table-column>
                <el-table-column
                        prop="wed"
                        :label="`星期三 (${weekStartDate.offset(2, cntWeek)})`"
                        width="150">
                    <template slot-scope="scope">
                        <div v-if="scope.row.wed.length > 0">
                            <div v-for="(item, index) in scope.row.wed" :key="index">
                                {{ item }}
                            </div>
                        </div>
                    </template>
                </el-table-column>
                <el-table-column
                        prop="thur"
                        :label="`星期四 (${weekStartDate.offset(3, cntWeek)})`"
                        width="150">
                    <template slot-scope="scope">
                        <div v-if="scope.row.thur.length > 0">
                            <div v-for="(item, index) in scope.row.thur" :key="index">
                                {{ item }}
                            </div>
                        </div>
                    </template>
                </el-table-column>
                <el-table-column
                        prop="fri"
                        :label="`星期五 (${weekStartDate.offset(4, cntWeek)})`"
                        width="150">
                    <template slot-scope="scope">
                        <div v-if="scope.row.fri.length > 0">
                            <div v-for="(item, index) in scope.row.fri" :key="index">
                                {{ item }}
                            </div>
                        </div>
                    </template>
                </el-table-column>
                <el-table-column
                        prop="sat"
                        :label="`星期六 (${weekStartDate.offset(5, cntWeek)})`"
                        width="150">
                    <template slot-scope="scope">
                        <div v-if="scope.row.sat.length > 0">
                            <div v-for="(item, index) in scope.row.sat" :key="index">
                                {{ item }}
                            </div>
                        </div>
                    </template>
                </el-table-column>
                <el-table-column
                        prop="sun"
                        :label="`星期日 (${weekStartDate.offset(6, cntWeek)})`"
                        width="150">
                    <template slot-scope="scope">
                        <div v-if="scope.row.sun.length > 0">
                            <div v-for="(item, index) in scope.row.sun" :key="index">
                                {{ item }}
                            </div>
                        </div>
                    </template>
                </el-table-column>
            </el-table>
            <div style="text-align: center; margin: 20px;">
                <div>
                    重复：{{conflictCount}} / 35, 空闲 {{35 - notBlankCount}} / 35.
                </div>
                <el-button-group>
                    <el-button type="primary" :disabled="cntWeek <= 1" @click="cntWeek -= 1;" icon="el-icon-arrow-left">
                        上一周
                    </el-button>
                    <el-button>第 {{ cntWeek }}/{{ weekCount }} 周</el-button>
                    <el-button type="primary" :disabled="cntWeek >= weekCount" @click="cntWeek += 1;">下一周
                        <i class="el-icon-arrow-right el-icon--right"></i></el-button>
                </el-button-group>
            </div>
        </div>

        <el-row :gutter="0" style="text-align: left;">
            <el-col :span="12">
                <el-table
                        :data="courseData">
                    <el-table-column
                            prop="id"
                            label="课程编号"
                            width="100">
                    </el-table-column>
                    <el-table-column
                            prop="name"
                            label="课程名称"
                            width="100">
                    </el-table-column>
                    <el-table-column
                            prop="teacher"
                            label="授课教师"
                            width="100">
                    </el-table-column>
                    <el-table-column
                            prop="place"
                            label="上课地点"
                            width="100">
                    </el-table-column>
                    <el-table-column
                            prop="size"
                            label="课时数目"
                            width="100">
                        <template slot-scope="scope">
                            {{ scope.row.assigned * 2 }} / {{ scope.row.size * 2 }}
                        </template>
                    </el-table-column>
                    <el-table-column
                            prop="ope"
                            label="操作"
                            width="100">
                        <template slot-scope="scope">
                            {{ scope.row.name }}
                            <div v-if="scope.row.assigned < scope.row.size">
                                <el-button type="text" size="small" @click="assignmentAdd(scope.row)">安排</el-button>
                                <el-button type="text" size="small" @click="courseRemove(scope.row)">删除</el-button>
                            </div>
                            <div v-if="scope.row.assigned >= scope.row.size">
                                <el-button type="text" @click="assignmentAdd(scope.row)" size="small" :disabled="true">
                                    已满
                                </el-button>
                                <el-button type="text" size="small" @click="courseRemove(scope.row)">删除</el-button>
                            </div>
                        </template>
                    </el-table-column>
                </el-table>
            </el-col>
            <el-col :span="12">
                <el-form ref="form" label-width="80px">
                    <el-form-item label="开始时间">
                        <el-date-picker style="width: 100%;"
                                        firstDayOfWeek="1"
                                        v-model="weekStartDate"
                                        placeholder="开始时间...">
                        </el-date-picker>
                    </el-form-item>
                    <el-form-item label="周数">
                        <el-input
                                v-model="weekCount"
                                type="number"
                                placeholder="周数...">
                        </el-input>
                    </el-form-item>
                    <div style="width: 100%; text-align: center;">
                        <div style="margin-bottom: 10px;">周数请选择周一的时间（否则同样认为是被选择的周的周一开始上课）</div>
                    </div>
                </el-form>
                <el-divider></el-divider>
                <el-form ref="form" label-width="80px">
                    <el-form-item label="课程编号">
                        <el-input
                                v-model="newCourse.id"
                                placeholder="课程编号...">
                        </el-input>
                    </el-form-item>
                    <el-form-item label="课程名称">
                        <el-input
                                v-model="newCourse.name"
                                placeholder="课程名称...">
                        </el-input>
                    </el-form-item>
                    <el-form-item label="授课教师">
                        <el-input
                                v-model="newCourse.teacher"
                                placeholder="授课教师...">
                        </el-input>
                    </el-form-item>
                    <el-form-item label="授课地点">
                        <el-input
                                v-model="newCourse.place"
                                placeholder="授课地点...">
                        </el-input>
                    </el-form-item>
                    <el-form-item label="课时数">
                        <el-input
                                v-model="newCourse.size"
                                type="number"
                                placeholder="课时数(偶数)...">
                        </el-input>
                    </el-form-item>
                    <div style="width: 100%; text-align: center;">
                        <el-button type="primary" @click="courseAdd">
                            添加课程
                        </el-button>
                    </div>
                </el-form>
                <el-dialog
                        title="提示"
                        :visible.sync="showModel"
                        width="30%">
                    <el-form ref="form" :model="assignment" label-width="80px">
                        <el-select v-model="assignment.day" placeholder="日期">
                            <el-option
                                    v-for="item in weekSelect"
                                    :key="item.value"
                                    :label="item.label"
                                    :value="item.value">
                            </el-option>
                        </el-select>
                        <el-select v-model="assignment.time" placeholder="时间">
                            <el-option
                                    v-for="item in timeSelect"
                                    :key="item.value"
                                    :label="item.label"
                                    :value="item.value">
                            </el-option>
                        </el-select>
                    </el-form>
                    <span slot="footer" class="dialog-footer">
                <el-button @click="showModel = false">取 消</el-button>
                <el-button type="primary" @click="showModel = false; modelCallback();">确 定</el-button>
                </span>
                </el-dialog>
            </el-col>
        </el-row>
    </div>
</template>

<script>

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

Date.prototype.offset = function (off, cntWeek) {
    var newDate = new Date(this);
    off += cntWeek * 7 - 7;
    if (this.getDay() === 0) { // sunday
        newDate.setDate(this.getDate() + off - 6);
    } else {
        newDate.setDate(this.getDate() + off - this.getDay() + 1);
    }
    return newDate.format("MM 月 dd 日");
}

export default {
    name: "course",
    data() {
        return {
            weekStartDate: new Date("2021-07-05"),
            weekCount: 4,
            cntWeek: 1,
            newCourse: {
                id: "CS000001",
                name: "课程",
                teacher: "教师",
                place: "上课地点",
                size: 2,
            },
            showModel: false,
            assignment: {
                name: "",
                teacher: "",
                day: 1,
                time: 1
            },
            notBlankCount: 0,
            conflictCount: 0,
            scheduleData: [
                {"time": "8:00 - 10:00", "mon": "", "tue": "", "wed": "", "thur": "", "fri": "", "sat": "", "sun": ""},
                {"time": "10:00 - 12:00", "mon": "", "tue": "", "wed": "", "thur": "", "fri": "", "sat": "", "sun": ""},
                {"time": "13:00 - 15:00", "mon": "", "tue": "", "wed": "", "thur": "", "fri": "", "sat": "", "sun": ""},
                {"time": "15:00 - 17:00", "mon": "", "tue": "", "wed": "", "thur": "", "fri": "", "sat": "", "sun": ""},
                {"time": "19:00 - 21:00", "mon": "", "tue": "", "wed": "", "thur": "", "fri": "", "sat": "", "sun": ""},
            ],
            courseData: [
                {"id": "CS000001", "name": "软件构造", "teacher": "教师", "place": "地点", "size": 3, "assigned": 0},
                {"id": "CS000001", "name": "软件构造", "teacher": "教师", "place": "地点", "size": 3, "assigned": 0},
            ],
            weekSelect: [
                {value: 1, label: "星期一"},
                {value: 2, label: "星期二"},
                {value: 3, label: "星期三"},
                {value: 4, label: "星期四"},
                {value: 5, label: "星期五"},
                {value: 6, label: "星期六"},
                {value: 7, label: "星期日"},
            ],
            timeSelect: [
                {value: 1, label: "8:00 - 10:00"},
                {value: 2, label: "10:00 - 12:00"},
                {value: 3, label: "13:00 - 15:00"},
                {value: 4, label: "15:00 - 17:00"},
                {value: 5, label: "19:00 - 21:00"}
            ]
        }
    },
    methods: {
        updateAllInfo() {
            let that = this;
            this.notBlankCount = 0;
            this.conflictCount = 0;
            axios.post("http://127.0.0.1:8899/course/getAllInfo").then(e => {
                let data = e.data.data;
                console.log(data);
                that.courseData = [];
                data.data.forEach(course => {
                    that.courseData.push({
                        "id": course.id,
                        "name": course.name,
                        "teacher": course.teacher,
                        "place": course.place,
                        "size": course.size,
                        "assigned": course.assigned,
                    })
                });
                // that.intervals = data.intervals;
                // that.blank = Number(data.blank);
                let temp = [];
                let weekDict = ["time", "mon", "tue", "wed", "thur", "fri", "sat", "sun"];
                for (let time = 1; time <= 5; time++) {
                    let obj = {};
                    let weekday = 0;
                    weekDict.forEach(item => {
                        if (item === "time") {
                            obj[item] = ["8:00 - 10:00", "10:00 - 12:00", "13:00 - 15:00", "15:00 - 17:00", "19:00 - 21:00"][time - 1];
                        } else {
                            weekday += 1;
                            data.intervals.forEach(course => {
                                let index = weekday * 5 - 5 + time;
                                if (obj[item] === undefined) {
                                    obj[item] = [];
                                }
                                if (Number(course.time) === index) {
                                    obj[item].push(`${course.label.name} ${course.label.teacher} @${course.label.place}`);
                                }
                            });
                        }
                    })
                    temp.push(obj);
                }
                for (let time = 0; time <= 4; time++) {
                    weekDict.forEach(item => {
                        if (item !== "time") {
                            if (temp[time][item].length > 0) {
                                this.notBlankCount += 1;
                            }
                            if (temp[time][item].length > 1) {
                                this.conflictCount += 1;
                            }
                        }
                    })
                }

                this.scheduleData = temp;
                console.log(temp);
            })
        },
        courseAdd() {
            axios.post("http://127.0.0.1:8899/course/courseAdd", {
                id: this.newCourse.id,
                name: this.newCourse.name,
                teacher: this.newCourse.teacher,
                place: this.newCourse.place,
                count: Number(this.newCourse.size),
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
        courseRemove(row) {
            axios.post("http://127.0.0.1:8899/course/courseRemove", {
                name: row.name,
                teacher: row.teacher,
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
        assignmentAdd(row) {
            this.assignment.name = row.name;
            this.assignment.teacher = row.teacher;
            this.showModel = true;
        },
        modelCallback() {
            axios.post("http://127.0.0.1:8899/course/assignmentAdd", {
                name: this.assignment.name,
                teacher: this.assignment.teacher,
                time: Number(this.assignment.time),
                day: Number(this.assignment.day)
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
        assignmentRemove() {

        },
    },
    mounted() {
        this.updateAllInfo();
    }
}
</script>

<style scoped>

</style>

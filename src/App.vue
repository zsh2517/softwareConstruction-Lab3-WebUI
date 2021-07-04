<template>
    <div id="app">
        <el-row :gutter="0">
            <el-col :span="4">
                <left-menu style="height: 100vh; border-right: 2px rgba(0, 0, 0, 0.2) solid;"></left-menu>
            </el-col>
            <el-col :span="20" style="overflow-y: scroll; height:100vh;">
                <div class="fullscreen-area" id="info">
                    <h1>说明</h1>
                    <div style="text-align: left; padding: 50px 100px;">
                        <div>
                            软件构造 Lab3 界面部分。
                        </div>
                        <el-divider></el-divider>
                        <div>
                            由于时间比较仓促，一些细节性的东西没有进一步的实现。<br>
                            目前界面部分包括了 duty, proces, course 三个模块的主体功能部分<br>
                            对于后续进一步的更改，没有在报告呈现
                        </div>
                        <el-divider></el-divider>
                        <div>技术栈</div>
                        <div>
                            前端：Vue.js 2, Element UI<br>
                            后端：Vert.X Web
                        </div>
                        <el-divider></el-divider>
                        <div style="text-align: center; font-size: x-large;">
                            <span>后端状态: </span>
                            <span v-if="backendReady" style="color: lightgreen">连接成功<span v-if="!firstSuccess"
                                                                                          style="color: red;">请刷新页面后使用</span></span>
                            <span v-if="!backendReady" style="color: red">连接失败</span>
                            <div v-if="!(backendReady && firstSuccess)">
                                <el-checkbox v-model="forceView">强制进入（不保证功能可用，仅供展示界面）</el-checkbox>
                            </div>
                        </div>
                        <el-divider></el-divider>
                        <div v-if="backendReady && firstSuccess" style="text-align: center;">
                            <div>↓ 向下翻页 ↓</div>
                            <div>↓ 向下翻页 ↓</div>
                            <div>↓ 向下翻页 ↓</div>
                        </div>
                        <div v-if="!backendReady" style="text-align: center;">
                            <div>请检查是否启动后端（/src/web/Main.java）</div>
                            <div>目前由于前端页面写死，暂时无法更换端口。请检查8899端口是否被占用</div>
                        </div>

                    </div>
                </div>
                <div class="fullscreen-area" id="duty" v-if="(backendReady && firstSuccess) || forceView">
                    <h1>排班管理</h1>
                    <duty></duty>
                </div>
                <hr>
                <div class="fullscreen-area" id="process" v-if="(backendReady && firstSuccess) || forceView">
                    <h1>进程管理</h1>
                    <process></process>
                </div>
                <hr>
                <div class="fullscreen-area" id="course" v-if="(backendReady && firstSuccess) || forceView">
                    <h1>课表管理</h1>
                    <course></course>
                </div>
            </el-col>
        </el-row>
    </div>
</template>

<script>
import leftMenu from './components/leftMenu'
import duty from './components/duty';
import process from './components/process';
import course from './components/course';

import axios from 'axios';

export default {
    name: 'App',
    components: {
        leftMenu,
        duty,
        process,
        course,
    },
    mounted() {
        axios.get("http://127.0.0.1:8899/active").then(e => {
            let data = e.data.data;
            if (data !== true && data !== "true") {
                this.backendReady = false;
                alert("与服务器连接失败");
            } else {
                this.backendReady = true;
            }
        }).catch(() => {
            this.backendReady = false;
            this.firstSuccess = false;
        });
        setInterval(() => {
            axios.get("http://127.0.0.1:8899/active").then(e => {
                let data = e.data.data;
                if (data !== true && data !== "true") {
                    this.backendReady = false;
                    alert("与服务器连接失败");
                } else {
                    this.backendReady = true;
                }
            }).catch(() => {
                this.backendReady = false;
                this.firstSuccess = false;
            });
        }, 5000);
    },
    data() {
        return {
            backendReady: false,
            firstSuccess: true,
            forceView: false
        }
    }
}
</script>

<style>
body {
    margin: 0;
}

.fullscreen-area {
    width: 100%;
    min-height: 100vh;
    box-sizing: border-box;
    padding: 10px;

}

#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;

}
</style>

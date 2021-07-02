<template>
    <div id="app">
        <el-row :gutter="0">
            <el-col :span="2">
                <left-menu style="height: 100vh;"></left-menu>

            </el-col>
            <el-col :span="22" style="overflow-y: scroll; height:100vh;">
                <div class="fullscreen-area" id="duty">
                    <h1>排班管理</h1>
                </div>
                <hr>
                <div class="fullscreen-area" id="process">
                    <h1>进程管理</h1>
                </div>
                <hr>
                <div class="fullscreen-area" id="course">
                    <h1>课表管理</h1>
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
export default {
    name: 'App',
    components: {
        leftMenu,
        duty,
        process,
        course
    }
}
</script>

<style>
body {
    margin: 0;
}

.fullscreen-area {
    width: 100%;
    height: 100vh;
}

#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;

}
</style>
